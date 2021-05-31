# 核心组件Pexpect

Pexpect是免费的，开源的。

PEXPECT LICENSE

> **该许可证已由OSI和FSF批准为GPL兼容。**
>
> ​	http://opensource.org/licenses/isc-license.txt
>
> Copyright (c) 2012, Noah Spurrier < [noah@noah.org](mailto:noah%40noah.org) > PERMISSION TO USE, COPY, MODIFY, AND/OR DISTRIBUTE THIS SOFTWARE FOR ANY PURPOSE WITH OR WITHOUT FEE IS HEREBY GRANTED, PROVIDED THAT THE ABOVE COPYRIGHT NOTICE AND THIS PERMISSION NOTICE APPEAR IN ALL COPIES. THE SOFTWARE IS PROVIDED “AS IS” AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.



Pexpect是一个用于生成子应用程序并自动控制它们的Python模块。Pexpect可用于自动化交互式应用程序，例如ssh，ftp，passwd，telnet等。它可用于在不同服务器上复制软件包进行自动化脚本安装。它还可以用于自动化软件测试。Pexpect遵循Don Libes的Expect精神，但是Pexpect是纯Python实现的。与其他类似Expect的python模块不同，Pexpect不需要TCL或Expect，也不需要编译C扩展。它可以在支持标准Python pty模块的任何平台上运行。Pexpect接口被设计为易于使用，因此简单的任务很容易。

Pexpect系统有两个主要接口；它们是`run()`函数和`spawn`类。spawn类功能很强大。run()函数比spawn类简单，但是对于快速调用程序很有用。当您调用run()函数时，它将执行一个给定的程序然后返回输出。这是`os.system()`的快速替代品。

例如：

```python
pexpect.run('ls -la')
```

Spawn类是Pexpect系统上功能强大的接口。您可以使用它来生成子程序，然后通过发送输入和期望响应（等待子程序输出中的模式）来与之交互。

例如：

```python
child = pexpect.spawn('scp foo user@example.com:.')
child.expect('Password:')
child.sendline(mypassword)
```

即使对于要求密码或普通stdin流之外的其他输入的命令，此命令也有效。例如，ssh直接从绕过stdin的TTY设备读取输入。

## spawn类

> ```python
> # spawn 类
> class pexpect.spawn(command, args=[], timeout=30, maxread=2000, searchwindowsize=None, logfile=None, cwd=None, env=None, ignore_sighup=False, echo=True, preexec_fn=None, encoding=None, codec_errors='strict', dimensions=None, use_poll=False)
> ```

----------------------

```python
# spawn主类接口
__init__（command，args = []，timeout = 30，maxread = 2000，searchwindowsize = None，logfile = None，cwd = None，env = None，ignore_sighup = False，echo = True，preexec_fn = None，encoding = None，codec_errors = 'strict'，Dimensions = None，use_poll = False ）
```

这是Pexpect的主类接口。使用此类来启动和控制子应用程序。

下面这段代码是在构造函数。这个函数的命令参数可以是命令及任何自带参数的命令的字符串。例如：

```python
child = pexpect.spawn('/usr/bin/ftp')
child = pexpect.spawn('/usr/bin/ssh user@example.com')
child = pexpect.spawn('ls -latr /tmp')
```

您还可以使用如下参数列表来构造它：

```python
child = pexpect.spawn('/usr/bin/ftp', [])
child = pexpect.spawn('/usr/bin/ssh', ['user@example.com'])
child = pexpect.spawn('ls', ['-latr', '/tmp'])
```

此后，将创建子应用程序并准备好与之对话。对于普通使用，请参见`Expect()`以及`send()`和`sendline()`。

请记住，Pexpect的不解释shell元字符，如重定向，管道或通配符（`>`，`|`，或`*`）。如果要运行命令并通过另一个命令传递它，则还必须启动shell。

例如：

```python
child = pexpect.spawn('/bin/bash -c "ls -l | grep LOG > logs.txt"')
child.expect(pexpect.EOF)
```

第二种形式的spawn（传递参数列表）在希望生成命令并传递自己的参数列表的情况下非常有用。这可以使语法更加清晰。例如，以下代码等同于上一个示例：

```python
shell_cmd = 'ls -l | grep LOG > logs.txt'
child = pexpect.spawn('/bin/bash', ['-c', shell_cmd])
child.expect(pexpect.EOF)
```

`maxread`属性设置读取缓冲区的大小。这是Pexpect一次尝试从TTY读取的最大字节数。将maxread大小设置为1将关闭缓冲。在从子程序读取大量输出的情况下，将maxread值设置得较高会提高性能。该功能与`searchwindowsize`结合使用时非常有用。

当关键字参数`searchwindowsize`的值为None（默认值）时，在每次接收输入数据时都将搜索全部缓冲区。在每次迭代中扫描的默认字节数非常大，可以减少以附带地减少搜索成本。在 [`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)函数返回结果后，无论`searchwindowsize`值是多少，缓冲区属性的大小都保持为`maxread`。

如果为关键字参数`timeout`指定一个数字（默认值：*30*），对于任何expect()方法调用，在指定的值（以秒为单位）过期后将触发超时。当参数值为None时，TIMEOUT不会触发超时，除非匹配到期望的值，否则 [`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)会一直阻塞。

`logfile`可以打开或关闭日志记录。所有输入和输出将被复制到指定的文件对象。将`logfile`设置为`None`可以停止日志记录（None是默认值）。将`logfile`设置为`sys.stdout`，可以将所有内容都送到标准输出。在每次进行写操作后都会刷新`logfile`。

日志输入和输出到文件的示例如下：

```python
child = pexpect.spawn('some_command')
fout = open('mylog.txt','wb')
child.logfile = fout
```

记录到stdout的示例：

```python
# 在 Python 2 中:
child = pexpect.spawn('some_command')
child.logfile = sys.stdout

# 在 Python 3 中, 我们将使用“encoding”参数对子进程中的数据进行解码，并将其作为unicode进行处理：
child = pexpect.spawn('some_command', encoding='utf-8')
child.logfile = sys.stdout
```

`logfile_read`和`logfile_send`可用于分别记录来自子程序的输入和发送给子程序的输出。有时，您不想看到您发送给子程序的内容。您只想记录子程序的返回结果。例如：

```python
child = pexpect.spawn('some_command')
child.logfile_read = sys.stdout
```

如果您使用的是Python 3，则需要使用`encoding`对传入spawn类中的字符串进行编码。

要单独记录发送到子程序的内容，请使用`logfile_send`：

```python
child.logfile_send = fout
```

如果`ignore_sighup`的值为True，则子进程将忽略SIGHUP信号。从Pexpect 4.0开始，`ignore_sighup`的默认值为False，这意味着SIGHUP将由子程序正常处理。

`delaybeforesend`有助于解决许多用户所遇到的一种奇怪行为。一个典型的问题就是，用户希望expect()出现`Password:`的提示，然后立即调用`sendline()`发送密码，然后用户会看到他们的密码被回显给他们。密码通常不会回显。由于大多数应用程序打印出“Password”提示后，会关闭stdin中的回显，但是如果您在应用程序关闭回显之前发送您的密码，那么您的密码就会被显示出来。通常，在真实键盘上与人互动时这不会成为问题。如果在写之前稍加延迟，则可以解决问题。对于许多用户来说，这是一个普遍的问题，因此我决定默认的pexpect行为应该是在写入子应用程序之前就进入睡眠状态。1/20秒（50毫秒）足以解决问题。您将`delaybeforesend`设置为`None`就可以回到以前的样子。

请注意，spawn在路径上查找命令时非常聪明。它使用与**which**查找可执行文件相同的逻辑。

如果希望获取子程序的退出状态，则必须调用close()方法。子程序的退出或信号状态存储在`self.exitstatus`或`self.signalstatus`中。如果子程序正常退出，则`exitstatus`将存储退出返回码，而`signalstatus`则为None。如果子程序被信号异常终止，则`signalstatus`将存储信号值，而`exitstatus`则为None：

```python
child = pexpect.spawn('some_command')
child.close()
print(child.exitstatus, child.signalstatus)
```

如果需要更多详细信息，还可以读取`self.status`成员，该成员存储`os.waitpid`返回的状态。您可以使用os.WIFEXITED/os.WEXITSTATUS或os.WIFSIGNALED/os.TERMSIG对此进行解读。

可以将echo属性设置为False来禁用输入的回显。作为一个伪终端，所有由“键盘”（send() 或者 sendline()）回响的输入都将被重复输出。在许多情况下，并不希望启用回显，并且以后可以使用setecho(False)和waitnoecho()将其禁用。但是，对于某些平台（例如Solaris），这是不可能的，应在生成时立即将其禁用。

如果指定了`preexec_fn`，它将在子进程中调用，然后启动指定的命令。这对于重置继承的信号处理程序很有用。

`dimensions`属性指定子进程看到的伪终端的大小，使用二元组（行，列）方式。如果未指定，则将使用ptyprocess中的默认值。

`use_poll`属性允许使用select.poll()来代替select.select()处理套接字。如果您的系统有大于1024个设备数据记录表，这将很方便。

```python
# expect函数
expect(pattern, timeout=-1, searchwindowsize=-1, async_=False, **kw)
```

此函数将在流中进行查找，直到匹配到指定的模式为止。该模式是重载的，可能有几种类型。该模式可以是字符串、EOF、正则表达式或由这些类型构成的列表。字符串将被编译为re类型。这会将索引返回给模式列表。如果模式不是列表，则在成功匹配时将返回索引0。这可能会导致EOF或TIMEOUT异常。为避免EOF或TIMEOUT异常，请将EOF或TIMEOUT添加到模式列表中。这将期望匹配EOF或TIMEOUT条件，而不是引发异常。

如果您传递一个模式列表并且有多个被匹配到，则会选择第一个匹配到的。如果同时匹配到多个模式，则选择模式列表中最左边的那个。例如：

```python
# 输入'foobar'
index = p.expect(['bar', 'foo', 'foobar'])
# 返回1('foo')，即使'foobar'是更好的匹配项
```

但是请注意，缓存会影响此行为，因为输入会以不可预测的形式到达。例如：

```python
# 输入'foobar'
index = p.expect(['foobar', 'foo'])
# 如果所有输入都同时到达则返回0('foobar'),
# 否则如果最后的“bar”部分到达迟了，则返回1('foo')
```

当找到给定模式的匹配项时，类实例属性的匹配项将成为re.MatchObject的结果。如果EOF或TIMEOUT被匹配到，则匹配到的属性将是异常类的实例。类实例属性之前和之后的配对是匹配模式之前和之后的数据视图。对于一般异常，类属性是该异常之前接收到的所有数据，而匹配到和之后的属性值均为None。

当关键字参数timeout的值为-1（默认值）时， timeout类属性的默认值被指定后TIMEOUT将被触发。当为None时，TIMEOUT将不会触发，并且会一直阻塞直到被匹配到。

当关键字参数searchwindowsize的值为-1（默认值）时，将使用由类maxread属性指定的值。

列表条目可以是EOF或TIMEOUT。这将捕获这些异常并返回列表条目的索引，而不引发异常。“after”属性将被设置为异常类型。“match”属性将为None。这使您可以编写如下代码：

```python
index = p.expect(['good', 'bad', pexpect.EOF, pexpect.TIMEOUT])
if index == 0:
    do_something()
elif index == 1:
    do_something_else()
elif index == 2:
    do_some_other_thing()
elif index == 3:
    do_something_completely_different()
```

而不是像这样的代码：

```python
try:
    index = p.expect(['good', 'bad'])
    if index == 0:
        do_something()
    elif index == 1:
        do_something_else()
except EOF:
    do_some_other_thing()
except TIMEOUT:
    do_something_completely_different()
```

这两种形式是等价的。这完全取决于你想要什么。如果您正在等待子进程的所有输出完成，那么您也可以只等待EOF。例如：

```python
p = pexpect.spawn('/bin/ls')
p.expect(pexpect.EOF)
print p.before
```

如果您想优化速度，请参考`expect_list()`。

在安装了asyncio的Python 3.4或Python 3.3上，传递 `async_=True` 将会返回一个异步进程，你可以从中得到与这个方法直接给出的结果相同的结果。因此，在进程内部，您可以替换以下代码：

```python
index = p.expect(patterns)
```

使用以下非阻塞形式替换上面代码：

```python
index = yield from p.expect(patterns, async_=True)
```

-----------------

```python
# expect_exact函数
expect_exact(pattern_list, timeout=-1, searchwindowsize=-1, async_=False, **kw)
```

这类似于expect()，但是是使用纯字符串匹配而不是“pattern_list”中的已编译正则表达式。“ pattern_list”可以是字符串、列表或其他字符串序列、或TIMEOUT和EOF。

此调用会比expect()快，这有两个原因：一是字符串搜索比RE匹配快，二是可以将搜索限制在输入缓冲区的末尾。

当您不用考虑转义要匹配的正则表达式字符时，此方法很有用。

和一样[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)，传递`async_=True`将使此返回异步协程。

------

```python
# expect_list函数
expect_list(pattern_list, timeout=-1, searchwindowsize=-1, async_=False, **kw)
```

这将获取已编译正则表达式的列表，并将索引返回到与子进程输出匹配的pattern_list中。该列表可以还包含EOF或TIMEOUT（它们不是已编译的正则表达式）。此方法与`expect()`方法类似，不同之处在于`expect_list()`不会在每次调用时重新编译模式列表。如果您要优化速度，这会有所帮助，否则只需使用`expect()`方法即可。这由`expect()`调用。

和一样[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)，传递`async_=True`将使此返回异步协程。

-----

```python
# compile_pattern_list函数
compile_pattern_list(patterns)
```

这将编译模式字符串或模式字符串列表。模式必须是 StringType、EOF、TIMEOUT、SRE_Pattern 或这些的列表。模式也可能是 None ，这会生成一个空列表（如果等待 EOF 或 TIMEOUT 条件而不期望其它任何模式，您可以这样做）。

此函数在调用`expect_list()`时，由`expect()`使用。因此，expect() 无非是：

```python
cpl = self.compile_pattern_list(pl)
return self.expect_list(cpl, timeout)
```

如果您在循环中使用 expect()，则先编译模式然后调用 expect_list() 可能更有效。这避免在循环中调用 compile_pattern_list()：

```python
cpl = self.compile_pattern_list(my_pattern)
while some_condition:
   ...
   i = self.expect_list(cpl, timeout)
   ...
```



------

```python
# send函数
send(s)
```

将字符串`s`发送到子进程，返回写入的字节数。如果指定了日志文件，则会将副本写入该日志。

默认终端输入模式是规范处理，除非子进程有其它设置。这允许在发送到接收程序之前执行退格和其他行处理操作。由于这是缓冲的，因此此类缓冲区的大小是有限制的。

在Linux系统上，该值为4096（由N_TTY_BUF_SIZE定义）。所有其他遵循 POSIX.1 定义的系统都支持，在OSX上为1024，OpenSolaris上为256，FreeBSD 上为1920。

可以使用`fpathconf(3) `找到该值：

```python
>>> from os import fpathconf
>>> print(fpathconf(0, 'PC_MAX_CANON'))
256
```

在上述代码的系统上，每行只能接收 256 个字节。超过这个值后的任何字节都将被丢弃。如果 tty 驱动程序设置了 IMAXBEL (termios.h)，则将BEL ( ) 发送到输出。通常默认情况下是启用此功能的。Linux 不将此作为一个选项。

在执行最终程序之前，可以通过执行 shell 和 stty(1) 来完全禁用规范输入处理：

```python
>>> bash = pexpect.spawn('/bin/bash', echo=False)
>>> bash.sendline('stty -icanon')
>>> bash.sendline('base64')
>>> bash.sendline('x' * 5000)
```

------

```python
# sendline函数
sendline(s='')
```

封装过的send()，将字符串`s`发送到子进程，并自动附加 `os.linesep`。返回写入的字节数。在默认终端模式下，每行只能发送有限数量的字节，详细信息请参考`send()`的文档。

------

```python
# write函数
write(s)
```

与send() 类似，只是没有返回值。

------

```python
# writelines函数
writelines(sequence)
```

这会为序列中的每个元素调用write()。序列可以是任何产生字符串的可迭代对象，通常是一个字符串列表。这不会添加行分隔符。没有返回值。

------

```python
# sendcontrol函数
sendcontrol(char)
```

将send()封装为包含助记符的帮助方法，用于向子进程发送控制字符（例如 Ctrl-C 或 Ctrl-D）。

例如，发送 Ctrl-G (ASCII 7, bell, 'g'):

```python
child.sendcontrol('g')
```

相关信息请参阅`sendintr()`和`sendeof()`。

------

```python
# sendeof函数
sendeof()
```

这会向子进程发送 EOF。这会发送一个将父进程输出缓冲区发送给处于等待状态的子进程的字符，而不是等待行尾。如果是该行的第一个字符，则用户程序中的 read() 返回 0，表示文件结束。这意味着要按预期工作，必须在行首调用 sendeof()。此方法不发送换行符。调用者有责任确保在一行的开头发送 eof。

------

```python
# sendintr函数
sendintr()
```

这会向子进程发送一个 SIGINT 信号。它不要求 SIGINT 是一行的第一个字符。

------

```python
#read()函数
read(size=-1)
```

这表示从文件中读取最多多少字节的数据。如果size参数为负数或省略，则读取所有数据，直到遇到EOF。字节作为字符串的返回对象。当遇到EOF时立即返回一个空字符串。

------

```python
# readline函数
readline(size=-1)
```

它读取并返回一整行。行尾的换行符作为字符串的一部分返回，除非文件结尾没有换行符。如果遇到EOF时立即返回一个空字符串。即使在UNIX上，它也会将CR/LF作为结束符查找新行，因为这是pseudotty设备返回的内容。因此与您可能期望的相反，您将收到rn为换行符的新行。

如果size参数为 0，则返回一个空字符串。在其他所有情况下size参数都会被忽略，这不是类文件对象的标准行为。

------

```python
# read_nonblocking函数
read_nonblocking(size=1, timeout=-1)
```

这表示从子进程中读取最多多少字符串的数。它包括timeout参数。如果读取未在超时时间内完成，则会引发TIMEOUT异常。如果读取到文件末尾，则会引发EOF异常。如果指定了日志文件，则会将副本写入该日志。

如果timeout的值为None，则读取可能会无限期的阻塞。如果超时为-1，则使用self.timeout的值。如果超时为 0，则轮询子进程，如果没有立即可读的数据，则会引发TIMEOUT异常。

超时指仅读取至少一个字符的时间量。这不受 'size' 参数的影响，因此如果您调用read_nonblocking(size=100, timeout=30) 并且只有一个字符立即可用，则将立即返回一个字符。它不会等待30秒再输入99个字符。

另一方面，如果有字节可以立即读取，则将读取所有这些字节（直至缓冲区装满）。因此，如果缓冲区大小为1M，并且有1M的数据可供读取，则无论超时如何，缓冲区都会被填充。

这是os.read()的封装。它使用select.select()或select.poll()来实现超时。

------

```python
# eof函数
eof()
```

如果触发了EOF 异常，则返回 True。

------

```python
# interact函数
interact( escape_character='\x1d' , input_filter=None , output_filter=None )
```

这将子进程的控制权交给交互式用户（键盘上的人）。键盘输入信息发送到子进程，并打印子进程的stdout和stderr输出。这只是将子进程stdout和子进程stderr发送到真正的stdout，并将真正的stdin发送到子stdin。当用户输入转义字符时，此方法将返回None。转义字符将不会被传输。转义字符的默认输入为`Ctrl - ]`，与 BSD telnet非常相似。为了防止转义，转义字符可以设置为 None。

如果指定了日志文件，则交互模式下从子进程发送和接收的数据将复制到指定日志中。

您可以传入可选的输入和输出过滤器函数。这些函数也应该接受字节数组并返回字节数组。即使支持使用`encoding='utf-8'`，interact也将始终传递input_filter和output_filter字节。您可能需要封装您的函数来解码并编码UTF-8。

output_filter将传递子进程的所有输出。input_filter将传递来自用户的所有键盘输入。input_filter在检查转义字符之前运行。

请注意，如果您更改父进程窗口的大小，SIGWINCH 信号将不会传递给子进程窗口。如果您希望进程窗口大小在父进程窗口大小更改时一起更改，请执行以下示例：

```python
import pexpect, struct, fcntl, termios, signal, sys
def sigwinch_passthrough (sig, data):
    s = struct.pack("HHHH", 0, 0, 0, 0)
    a = struct.unpack('hhhh', fcntl.ioctl(sys.stdout.fileno(),
        termios.TIOCGWINSZ , s))
    if not p.closed:
        p.setwinsize(a[0],a[1])

# 注意这个'p'是全局的，用于sigwinch_passthrough.
p = pexpect.spawn('/bin/bash')
signal.signal(signal.SIGWINCH, sigwinch_passthrough)
p.interact()
```

------

```python
# logfile函数
logfile
```

```python
# logfile_read函数
logfile_read
```

```python
# logfile_send函数
logfile_send
```

将这些设置为Python文件对象（或[`sys.stdout`](https://docs.python.org/3/library/sys.html#sys.stdout)）以记录所有交互信息、从子进程读取的数据或发送到子进程的数据。

> 当spawn处于bytes模式时，日志文件应该以二进制方式打开。在unicode模式下，它们应该打开以编写unicode文本。请参见处理unicode。

### 控制子进程

> ```python
> # spawn的主进程
> class pexpect.spawn
> ```

------

```python
# pexpect.spawn.kill(sig)
kill(sig)
```

这会将指定的信号发送到子应用程序。为了与UNIX传统保持一致，它有一个误导性的名称。它不一定会杀死子程序，除非你发出正确的信号。

------

```python
# pexpect.spawn.terminate(force=False)
terminate(force=False)
```

强制子进程终止。

从SIGHUP和SIGINT开始。如果“ force”的值为True，则转到SIGKILL。如果子进程被终止，则返回True。如果子进程无法终止，则返回False。

------

```python
# pexpect.spawn.isalive()
isalive()
```

测试子进程是否存活。这是非阻塞的。如果子进程被终止，那么将读取子进程的`exitstatus`或者`signalstatus`。如果子进程还存活，则返回True，否则返回False。Solaris返回正确的状态可能需要几秒钟的时间。

------

```python
# pexpect.spawn.wait()
wait()
```

等待子进程退出。这是一个阻止呼叫。它不会从子进程读取任何数据，因此，如果子进程具有未读的输出并已退出，则它将永远阻塞。换句话说，子进程可能已经打印了输出并执行exit()函数，但是在父进程读取其输出之前，子进程仍然是存活的。

如果之前已调用wait()，或者isalive()方法返回False，则此方法是非阻塞的。它只返回先前确定的退出状态。

------

```python
# pexpect.spawn.close(force=True)
close(force=True)
```

关闭与子进程的连接。请注意，多次调用close()是有效的。这模拟了文件的标准Python行为。如果要确保终止子进程，请将force设置为True（如果子进程忽略SIGHUP和SIGINT，则发送SIGKILL）。

------

```python
# pexpect.spawn.getwinsize()
getwinsize()
```

返回子tty的终端窗口大小。返回值是（行，列）的元组。

------

```python
# pexpect.spawn.setwinsize(rows, cols)
setwinsize(rows, cols)
```

设置子tty的终端窗口大小。这会将SIGWINCH信号发送给子进程。这不会更改物理窗口的大小。它更改窗口大小后会报告给感知TTY的应用程序（如vi或curses），这些应用程序响应SIGWINCH信号。

------

```python
# pexpect.spawn.getecho()
getecho()
```

返回终端的回显模式。如果echo打开，则返回True；如果echo关闭，则返回False。期望您输入密码的子进程通常将ECHO设置为False。请参见waitnoecho()。

在isatty()返回False的平台上不支持。

------

```python
# pexpect.spawn.setecho(state)
setecho(state)
```

打开或关闭终端回显模式。请注意，在回显之前发送给子进程的所有内容都会丢失，因此在调用setecho()之前，应确保输入缓冲区为空。例如，以下代码将按预期执行：

- 

  ```python
  p = pexpect.spawn('cat') # Echo is on by default.
  p.sendline('1234') # We expect see this twice from the child...
  p.expect(['1234']) # ... once from the tty echo...
  p.expect(['1234']) # ... and again from cat itself.
  p.setecho(False) # Turn off tty echo
  p.sendline('abcd') # We will set this only once (echoed by cat).
  p.sendline('wxyz') # We will set this only once (echoed by cat)
  p.expect(['abcd'])
  p.expect(['wxyz'])
  ```

以下代码将不起作用，因为在setecho之前发送的行将丢失：

- ```python
  p = pexpect.spawn('cat')
  p.sendline('1234')
  p.setecho(False) # Turn off tty echo
  p.sendline('abcd') # We will set this only once (echoed by cat).
  p.sendline('wxyz') # We will set this only once (echoed by cat)
  p.expect(['1234'])
  p.expect(['1234'])
  p.expect(['abcd'])
  p.expect(['wxyz'])
  ```

在isatty()返回False的平台上不支持。

------

```python
# pexpect.spawn.waitnoecho(timeout=-1)
waitnoecho(timeout=-1)
```

等待直到终端ECHO标志设置为False。如果回显模式关闭则返回True。如果在超时之前未将ECHO标志设置为False，则返回False。子进程在等待密码输入时可用于检测。通常，子进程在等待用户输入密码时会关闭回显模式。例如，您可以等待子进程将ECHO设置为off，而不需要等待出现“password:”提示：

- ```python
  p = pexpect.spawn('ssh user@example.com')
  p.waitnoecho()
  p.sendline(mypassword)
  ```

 如果timeout ==-1，则此方法将使用self.timeout中的值。如果timeout == None，则此方法将阻塞直到ECHO标志为False。

------

```python
# pexpect.spawn.pid
pid
```

子进程的进程ID。

------

```python
# pexpect.spawn.child_fd
child_fd
```

用于与子进程进行通信的文件描述符。




### 处理unicode

默认情况下，[`spawn`](#spawn类)是一个字节接口：它的read方法返回字节，write/send和expect方法则等待字节。如果将*encoding* 参数传递给构造函数，则它将充当unicode接口：您发送的字符串将使用该编码进行编码，接收的字节将在返回给您之前进行解码。在此模式下，[`expect()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect)和[`expect_exact()`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn.expect_exact)应为unicode。

*自版本4.0以后：*[`spawn`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn)类提供字节和unicode接口。在Pexpect 3.x中，unicode接口由一个单独的`spawnu`类提供。

为了向后兼容，某些Unicode允许采用bytes模式：send方法将任意Unicode编码为UTF-8，然后再将其发送到子进程，其expect方法只能接受ascii-only Unicode字符串。

> 尽管名称不同，但在Python 2和3上使用pexpect进行Unicode处理的工作方式是相同的。
>
> - Bytes模式在Python 2上使用`str`，在Python 3使用`bytes`
> - Unicode模式Python 2上使用`unicode`，在Python 3使用`str`



## run函数

```python
pexpect.run(command, timeout=30, withexitstatus=False, events=None, extra_args=None, logfile=None, cwd=None, env=None, **kwargs)
```

该函数运行给定的命令；等待它完成；然后以字符串形式返回所有输出。STDERR包含在输出中。如果未提供命令的完整路径，则会搜索路径。

请注意，即使在类UNIX系统上，行也由CR/LF组合终止，因为这是伪tty的标准。如果将`withexitstatus`设置为true，则run将返回一个元组（command_output，exitstatus）。如果`withexitstatus`为false，则只返回command_output。

通常可以使用run()函数来代替spawn()创建spawn实例。例如，以下代码使用spawn：

```python
from pexpect import *
child = spawn('scp foo user@example.com:.')
child.expect('(?i)password')
child.sendline(mypassword)
```

上面的代码可以替换为以下代码：

```python
from pexpect import *
run('scp foo user@example.com:.', events={'(?i)password': mypassword})
```

**示例**

在本地计算机上启动apache守护程序：

```python
from pexpect import *
run("/usr/local/apache/bin/apachectl start")
```

使用SVN运行文件：

```python
from pexpect import *
run("svn ci -m 'automatic commit' my_file.py")
```

运行命令并捕获退出状态：

```python
from pexpect import *
(command_output, exitstatus) = run('ls -l /bin', withexitstatus=1)
```

下面是运行SSH并在远程计算机上执行“ls -l”。如果出现**(?i)password**模式，则发送密码**secret**：

```python
run("ssh username@machine.example.com 'ls -l'",
    events={'(?i)password':'secret\n'})
```

如下示例是启动mencoder来从DVD翻录视频，还将在运行时每5秒显示一次进度：

```python
from pexpect import *
def print_ticks(d):
    print d['event_count'],
run("mencoder dvd://1 -o video.avi -oac copy -ovc copy",
    events={TIMEOUT:print_ticks}, timeout=5)
```

“事件”参数应该是包含模式和响应的字典或元组列表。每当在命令输出中看到其中一种模式时，run()将发送关联的响应字符串。因此，以上示例中的run()也可以写为：

```python
run("mencoder dvd://1 -o video.avi -oac copy -ovc copy",events=[(TIMEOUT,print_ticks)], timeout=5)
```

如果命令输出需要对应该匹配的模式进行精细控制，则使用事件的元组列表，因为元组列表将作为其模式列表传递给pexpect()，并保留模式的顺序。

请注意，如果需要使用Enter，则应在字符串中输入换行符。

像上面的示例一样，响应也可能包含回调，函数或方法。它应该接受字典值作为参数。字典包含run()函数中的所有本地变量，因此您可以访问子程序对象或run()函数中定义的任何其他变量（event_count，child和extra_args最常用）。回调可能返回True来停止当前的运行过程。否则run()会继续运行直到下一个事件出现。回调还可能返回一个字符串，该字符串将发送给子程序并直接调用run()而不使用“ extra_args”。它提供了一种将数据传递给回调函数的方法。

像[`spawn`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn)一样，传递编码将使其使用unicode而不是bytes。您可以传递*codec_errors*来控制如何处理编码和解码中的错误。



## 异常情况

```python
class pexpect.EOF(value)
```

当从子程序那里读到EOF时触发。这通常意味着子程序已经退出。

```python
class pexpect.TIMEOUT(value)
```

当读取时间超时时触发。

```python
class pexpect.ExceptionPexpect(value)
```

此模块是触发的所有异常类的基类。



## 实用功能

```python
pexpect.which(filename, env=None)
```

这需要一个给定的文件名；尝试在环境路径中找到它；然后检查它是否可执行。如果找到文件并可执行，它将返回文件名的完整路径。否则，将返回None。

```python
pexpect.split_command_line(command_line)
```

这会将命令行拆分为参数列表。它使用空格为关键字拆分参数，可以处理嵌入的引号、双引号和转义字符。用正则表达式不可能做到这一点，所以我编写了一个小型状态机来解析命令行。