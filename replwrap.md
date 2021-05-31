# replwrap - 控制 read-eval-print-loops

read-eval-print-loops 的通用封装器，又名交互式shell。

*3.3 版中的新功能。*

## fdspawn类

> ```python
> # replwrap类
> class pexpect.replwrap.REPLWrapper(cmd_or_spawn, orig_prompt, prompt_change, new_prompt='[PEXPECT_PROMPT>', continuation_prompt='[PEXPECT_PROMPT+', extra_init_cmd=None)
> ```

REPL的封装器。

参数：

- **cmd_or_spawn** – 这可以是[`pexpect.spawn`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn) 已经启动REPL的实例，也可以是启动新REPL进程的str命令。
- **orig_prompt** ( [*str*](https://docs.python.org/3/library/stdtypes.html#str) ) – 最初期望的提示。
- **prompt_change** ( [*str*](https://docs.python.org/3/library/stdtypes.html#str) ) – 将提示更改为更独特的命令。如果是`None`，则不会更改提示。这将使用 new 和 continuation 提示作为位置参数进行格式化，因此您可以使用`{}`样式格式将它们插入到命令中。
- **new_prompt** ( [*str*](https://docs.python.org/3/library/stdtypes.html#str) ) – 更改后期望的更独特的提示。
- **extra_init_cmd**（[*str*](https://docs.python.org/3/library/stdtypes.html#str)）–执行额外的初始化命令，例如禁用寻呼机。



------

```python
# run_command函数
run_command(command, timeout=-1, async_=False)
```

向REPL发送命令，等待并返回输出。

参数：

- **command**（[*str*](https://docs.python.org/3/library/stdtypes.html#str)）– 要发送的命令。不需要跟随换行符。这应该触发执行一个完整的输入；如果在发送输入后发现继续提示，将触发[`ValueError`](https://docs.python.org/3/library/exceptions.html#ValueError) 。
- **timeout** ( [*int*](https://docs.python.org/3/library/functions.html#int) ) – 超时时间。-1表示[`pexpect.spawn`](https://pexpect.readthedocs.io/en/stable/api/pexpect.html#pexpect.spawn)对象的默认值（默认30秒）。None 意味着一直等待。
- **async** ( [*bool*](https://docs.python.org/3/library/functions.html#bool) ) – 在安装了asyncio的Python 3.4或Python 3.3上，传递 `async_=True` 将会返回一个异步进程，你可以从中得到与这个方法直接给出的结果相同的结果。

------

```python
# pexpect.replwrap.PEXPECT_PROMPT
pexpect.replwrap.PEXPECT_PROMPT
```

可用作提示的字符串，不太可能在输出中找到。

使用上面的对象，很容易包装一个 REPL。例如，要使用 Python shell：

```python
py = REPLWrapper("python", ">>> ", "import sys; sys.ps1={!r}; sys.ps2={!r}")
py.run_command("4+7")
```

为 Python和bash shell提供了便利功能，如下：

------

```python
# pexpect.replwrap.python
pexpect.replwrap.python(command='python')
```

启动Python-shell并返回一个[`REPLWrapper`](https://pexpect.readthedocs.io/en/stable/api/replwrap.html#pexpect.replwrap.REPLWrapper)对象。

------

```python
# pexpect.replwrap.bash
pexpect.replwrap.bash(command='bash')
```

启动一个bash-shell 并返回一个[`REPLWrapper`](https://pexpect.readthedocs.io/en/stable/api/replwrap.html#pexpect.replwrap.REPLWrapper)对象。