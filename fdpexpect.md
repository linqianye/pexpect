# fdpexpect - 将pexpect与文件描述符一起使用

和pexpect类似，但它适用于您传递给它的任何文件描述符。您负责打开和关闭文件描述符。这允许您将Pexpect与套接字和命名管道 (FIFO) 一起使用。

PEXPECT LICENSE

> **该许可证已由OSI和FSF批准为GPL兼容。**
>
> http://opensource.org/licenses/isc-license.txt
>
> Copyright (c) 2012, Noah Spurrier <[noah@noah.org](mailto:noah%40noah.org)> PERMISSION TO USE, COPY, MODIFY, AND/OR DISTRIBUTE THIS SOFTWARE FOR ANY PURPOSE WITH OR WITHOUT FEE IS HEREBY GRANTED, PROVIDED THAT THE ABOVE COPYRIGHT NOTICE AND THIS PERMISSION NOTICE APPEAR IN ALL COPIES. THE SOFTWARE IS PROVIDED “AS IS” AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.



## fdspawn类

> ```python
> # fdspawn类
> class pexpect.fdpexpect.fdspawn(fd, args=None, timeout=30, maxread=2000, searchwindowsize=None, logfile=None, encoding=None, codec_errors='strict', use_poll=False)
> ```

Bases： `pexpect.spawnbase.SpawnBase`

类似pexpect.spawn，但允许您提供自己的打开文件描述符。例如，您可以使用它来读取文件以查找模式，或者控制调制解调器或串行设备。

------

```python
# fdspawn类主接口
__init__(fd, args=None, timeout=30, maxread=2000, searchwindowsize=None, logfile=None, encoding=None, codec_errors='strict', use_poll=False)
```

这是一个文件描述符（int）或一个支持 fileno() 方法（返回int）的对象。所有Python类文件对象都支持fileno()。

------

```python
# isalive函数
isalive()
```

检查文件描述符是否仍然存活。如果[`os.fstat()`](https://docs.python.org/3/library/os.html#os.fstat) 没有触发异常，那么我们假定它是存活着的。

------

```python
# close函数
close()
```

关闭文件描述符。

第二次调用此方法不会产生任何效果，但是如果文件描述符在其他地方被关闭，则会触发[`OSError`](https://docs.python.org/3/library/exceptions.html#OSError)。

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