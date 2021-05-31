# API概述

Pexpect可用于自动化交互式应用程序，例如ssh，ftp，mencoder，passwd等。Pexpect接口被设计为易于使用。

以下是一个实际的Pexpect示例：

```python
# 连接到openbsd ftp站点
# 下载递归目录列表
import pexpect
child = pexpect.spawn('ftp ftp.openbsd.org')
child.expect('Name .*: ')
child.sendline('anonymous')
child.expect('Password:')
child.sendline('noah@example.com')
child.expect('ftp> ')
child.sendline('lcd /tmp')
child.expect('ftp> ')
child.sendline('cd pub/OpenBSD')
child.expect('ftp> ')
child.sendline('get README')
child.expect('ftp> ')
child.sendline('bye')
```

显然，您可以使用Python自己的[`ftplib`](https://docs.python.org/3/library/ftplib.html#module-ftplib)模块编写一个ftp客户端，但这只是一个演示。您可以在任何应用程序中使用此技术。如果您正在编写自动化测试工具，这将特别方便。

在Pexpect中有两种重要的方法–[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)和 [`send()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.send)（或[`sendline()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.sendline)，类似于[`send()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.send)加上换行符）。

[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect) 方法是等待子应用程序返回指定的字符串。您指定的字符串是一个正则表达式，因此您可以进行复杂的模式匹配。

 [`send()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.send)方法是将字符串写入子应用程序。从子程序的的角度看，就像有人在终端上键入文本一样。

每次调用[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)后的`before`和`after` 属性将被设置为子应用程序打印的文本。`before` 属性将包含所有文本，直到期望的字符串模式为止；`after` 字符串将包含与预期模式匹配的文本。match属性设置为[重新匹配对象](http://docs.python.org/3/library/re#match-objects)。

用一个Pexpect的例子可能会使事情变得更清楚。本示例使用ftp登录到OpenBSD站点，然后列出目录中的文件；再将ftp会话的交互式控制传递给人类用户：

```python
import pexpect
child = pexpect.spawn ('ftp ftp.openbsd.org')
child.expect ('Name .*: ')
child.sendline ('anonymous')
child.expect ('Password:')
child.sendline ('noah@example.com')
child.expect ('ftp> ')
child.sendline ('ls /pub/OpenBSD/')
child.expect ('ftp> ')
print child.before   # 打印ls命令的结果。
child.interact()     # 将子程序的控制权交给用户。
```



# 两种特殊的模式：EOF和TIMEOUT

有两种特殊的模式可以匹配：文件结束（[`EOF`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.EOF)）或超时条件（[`TIMEOUT`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.TIMEOUT)）。您可以将这些模式传递给[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)，这些模式不是正则表达式，像预定义常量一样使用它们即可。

如果子程序已经死亡，并且您已经通过 [`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)读取了子程序的所有输出，则会引发[`EOF`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.EOF)异常。您可以使用EOF模式读取EOF之前的所有内容，而不会产生异常。在这种情况下子程序输出的所有内容都将在`before`属性中，也可以正常使用。

[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)的模式可以是正则表达式，也可以是正则表达式列表。这使您可以匹配多个可选响应。[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)方法会返回被匹配到的模式的索引。例如，您要登录到服务器，输入密码后，您可以从服务器获得各种响应——您的密码可能会被拒绝；或者可以允许您进入并询问您的终端类型；或者您可以直接进入并给出命令提示符。

下面的代码片段给出了一个示例：

```python
child.expect('password:')
child.sendline(my_secret_password)
# 我们期望这三种模式中的任何一种...
i = child.expect (['Permission denied', 'Terminal type', '[#\$] '])
if i==0:
    print('Permission denied on host. Can\'t login')
    child.kill(0)
elif i==1:
    print('Login OK... need to send terminal type.')
    child.sendline('vt100')
    child.expect('[#\$] ')
elif i==2:
    print('Login OK.')
    print('Shell command prompt', child.after)
```

如果没有任何东西符合预期的模式，那么[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)最终将引发[`TIMEOUT`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.TIMEOUT)异常。默认时间是30秒，但是您可以更改[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)的超时时间：

```python
# 等待密码提示不超过2分钟（120秒）。
child.expect('password:', timeout=120)
```



# 确定行尾 – CR/LF

Pexpect对正则表达式的匹配与您习惯的使用稍有不同。

`$`进行行尾模式匹配是无用的。Pexpect一次从子程序中只读取一个字符，因此每个字符都看起来像行尾。Pexpect无法对子程序的输出流进行预读。通常将正则表达式与任意流一起使用时会遇到这种情况。

>Pexpect确实具有一个内部缓冲区，因此读取一次的速度比一个字符快，但是从用户的角度来看，正则表达式模式测试一次只发生一个字符。

匹配行尾的最佳方法是查找换行符：`"\r\n"` （CR/LF）。是的，这似乎是DOS风格的。某些UNIX人士可能会惊讶地发现，终端TTY设备驱动程序（dumb，vt100，ANSI，xterm等）都使用CR/LF组合来表示行尾。Pexpect使用Pseudo-TTY设备与子应用程序对话，因此当子应用程序打印`"\n"` 时您实际上会看到`"\r\n"`。

UNIX仅使用换行符来结束文本行，但在TTY设备上则不使用换行符！TTY设备更像Windows。每行文本以CR/LF组合结尾。当您从TTY设备截取UNIX命令中的数据时，您会发现TTY设备输出了CR/LF组合。UNIX命令只能写一个换行符（`\n`），但是TTY设备驱动程序会将其转换为CR/LF。这意味着您的终端将看到以CR/LF（hex `0D 0A`）结尾的行。由于Pexpect是模拟终端，因此要匹配行尾，您必须期望CR/LF组合出现：

```python
child.expect('\r\n')
```

如果只需要跳过新行，`expect('\n')`则可以单独使用，但是如果期望在行尾之前使用特定模式，则需要显式查找`\r`。例如，以下代码期望在行尾出现一个单词：

```python
child.expect('\w+\r\n')
```

但是以下情况会失败：

```python
child.expect('\w+\n')
```

并且如前所述，尝试使用`$`以匹配行尾也将不起作用：

```python
child.expect ('\w+$')
```

因此，如果需要显式查找行尾，则需要查找CR/LF组合，而不仅是LF或者$模式。

此问题不仅限于Pexpect。每当您尝试在流上执行正则表达式匹配时，都会发生此问题。正则表达式需要预读，而对于流则很难进行预读，因为生成流的过程可能还没有完成，无法知道该过程是暂时暂停还是已结束并处于等待状态。Pexpect必须在输入的末尾隐式始终执行非贪婪匹配（最小匹配）。

Pexpect编译带有`re.DOTALL`标志的所有正则表达式。对于[`DOTALL`](https://docs.python.org/3/library/re.html#re.DOTALL)标志，`"."`将匹配换行符。

# 注意模式末尾的+和*

请记住，每当您尝试匹配需要预读的模式时，总会得到最小匹配（非贪婪）。例如，以下内容将始终仅返回一个字符：

```
child.expect ('.+')
```

此示例将成功匹配，但将始终不返回任何字符：

```
child.expect ('.*')
```

通常，任何星号*表达式都将尽可能少地匹配。

您可以做的一件事是尝试在`\d+`模式结尾处强制使用不模糊的字符。期望该字符分隔字符串。例如，您可以尝试将模式的结尾由`\D*`改为`\D+`。仅数字位数将无法满足`(\d+)\D+` 模式。您将需要一些数字和至少一个非数字存在于结束位置。

# 调试

如果获得[`pexpect.spawn`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn)对象的字符串值，则将获得许多有用的调试信息。使用以下模式对于调试非常有用：

```python
try:
    i = child.expect ([pattern1, pattern2, pattern3, etc])
except:
    print("Exception was thrown")
    print("debug information:")
    print(str(child))
```

将子程序的输入和输出记录到文件或屏幕上也很有用。以下例子是打开日志记录功能并将输出信息发送到标准输输出（屏幕）：

```python
child = pexpect.spawn(foo)
child.logfile = sys.stdout.buffer
```

从Python3开始，`sys.stdout.buffer`对象就可用了。对于Python2，必须使用`sys.stdout`。



# 特殊情况

## [EOF](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.EOF)

注意，可能会抛出两种类型的EOF异常。除了消息字符串外，它们实际上是相同的。在实际使用中您不需要区分它们，但它们确实提供了一些关于您正在运行的平台类型的额外信息。这两条信息是：

- “ read()中的文件结尾(EOF)：异常样式平台。”
- “ read()中的文件结尾(EOF)：空的字符串样式平台。”

当您尝试从EOF状态的文件描述符中读取时，某些UNIX平台将引发异常。其他UNIX平台则悄悄地返回一个空字符串，以指示已达到EOF状态。

如果您希望在不产生任何[`EOF`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.EOF)异常的情况下读取子程序的输出的结尾，请使用`expect(pexpect.EOF)`方法。

## [TIMEOUT](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.TIMEOUT)

如果子程序在给定的时间内没有生成任何输出，expect()和read()方法也将超时。如果发生这种情况，它们将引发`TIMEOUT`异常。通过为timeout参数传递None，可以让这些方法忽略超时并无限期的阻塞 ：

```python
child.expect(pexpect.EOF, timeout=None)
```



# Windows上的Pexpect

*版本4.0中的新功能：* 支持Windows

可以在Windows上使用Pexpect来等待子进程（使用[`pexpect.popen_spawn.PopenSpawn`](https://pexpect.readthedocs.io/en/stable/api/popen_spawn.html#pexpect.popen_spawn.PopenSpawn)，或使用[`pexpect.fdpexpect.fdspawn`](https://pexpect.readthedocs.io/en/stable/api/fdpexpect.html#pexpect.fdpexpect.fdspawn)的文件描述符）。

[`pexpect.spawn`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn)和[`pexpect.run()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.run)在Windows上不可用，因为它们依赖Unix伪终端（ptys）。跨平台代码不能使用这些函数。

`PopenSpawn`不能直接替代`spawn`。许多程序只有在检测到它们正在终端中运行时，才提供交互行为。当运行`PopenSpawn`时，它们的行为可能会有所不同。