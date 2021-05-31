# Pexpect 版本 4.8

Pexpect是让Python成为能更好的控制其他应用程序的工具。

Pexpect是一个用于生成、控制和响应子应用程序的纯Python模块。Pexpect和Don Libes的Expect很类似。Pexpect允许您的脚本生成子应用程序并对其进行控制，就像人在键入命令一样。

Pexpect可用于自动化交互式应用程序，例如ssh，ftp，passwd，telnet等。它可用于在不同服务器上复制软件包进行自动化脚本安装。它还可以用于自动化软件测试。Pexpect遵循Don Libes的Expect精神，但是Pexpect是纯Python实现的。与其他类似Expect的python模块不同，Pexpect不需要TCL或Expect，也不需要编译C扩展。它可以在支持标准Python pty模块的任何平台上运行。Pexpect接口被设计为易于使用。

内容：

- [安装](安装.md)
  
  - [安装](安装.md#安装)
  - [要求](安装.md#要求)
- [API概述](API概述.md)
  
  - [两种特殊的模式：EOF和TIMEOUT](API概述.md#两种特殊的模式：EOF和TIMEOUT)
  - [用CR/LF确定行尾](API概述.md#用CR/LF确定行尾)
  - [注意模式末尾的+和*](API概述.md#注意模式末尾的+和*)
  - [调试](API概述.md#调试)
  - [特殊情况](API概述.md#特殊情况)
  - [Windows上的Pexpect](API概述.md#Windows上的Pexpect)
- API文档
  - [核心组件Pexpect](pexpect.md)
  - [fdpexpect-将pexpect与文件描述符一起使用](fdpexpect.md)
  - [popen_spawn-将pexpect与管道子进程一起使用](popen_spawn.md)
  - [replwrap-控制读取评估打印循环](replwrap.md)
  - [pxssh-控制SSH会话](pxssh.md)
- [例子](examples.md)
- [常问问题](https://pexpect.readthedocs.io/en/stable/FAQ.html)

  