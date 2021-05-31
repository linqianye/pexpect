# popen_spawn - 将 pexpect 与管道子进程一起使用

使用 subprocess.Popen 提供类似 pexpect.spawn 接口的接口

## PopenSpawn 类

> ```python
> class pexpect.popen_spawn.PopenSpawn(cmd, timeout=30, maxread=2000, searchwindowsize=None, logfile=None, cwd=None, env=None, encoding=None, codec_errors='strict', preexec_fn=None)
> ```

------

```python
# 主类接口
__init__(cmd, timeout=30, maxread=2000, searchwindowsize=None, logfile=None, cwd=None, env=None, encoding=None, codec_errors='strict', preexec_fn=None)
```

初始化PopenSpawn。请参阅 help(type(self)) 以获得准确的签名。

------

```python
# send函数
send(s)
```

将数据发送到子进程的标准输入。返回写入的字节数。

------

```python
# sendline函数
sendline(s='')
```

封装过的send()，将字符串`s`发送到子进程，并自动附加 `os.linesep`。返回写入的字节数。

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
# kill函数
kill(sig)
```

向子进程发送Unix信号。

使用信号模块中的常量指定是哪个信号。

------

```python
# sendeof函数
sendeof()
```

在写入结束后关闭stdin管道。

------

```python
# wait函数
wait()
```

等待子进程完成。返回退出代码。

------

```python
# expect函数
expect()
```

```python
# expect_exact函数
expect_exact()
```

```python
# expect_list函数
expect_list()
```

和[`pexpect.spawn`](pexpect.md#pexpect.spawn)一样。