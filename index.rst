Pexpect版本4.8
=========================

Pexpect使Python成为控制其他应用程序的更好工具。

Pexpect是用于生成子应用程序的纯Python模块。控制他们；并响应其输出中的预期模式。Pexpect的作品就像Don Libes的Expect。Pexpect允许您的脚本生成子应用程序并对其进行控制，就像人类在键入命令一样。

Pexpect可用于自动化交互式应用程序，例如ssh，ftp，passwd，telnet等。它可用于自动化安装脚本，以在不同服务器上复制软件包安装。它可以用于自动化软件测试。Pexpect遵循Don Libes的Expect的精神，但Pexpect是纯Python。与其他类似Python的类似Expect的模块不同，Pexpect不需要TCL或Expect，也不需要编译C扩展。它应该可以在支持标准Python pty模块的任何平台上运行。Pexpect界面设计为易于使用。

内容：

.. toctree::
   :maxdepth: 2
   
   安装
   要求
   API概述
   特殊的EOF和TIMEOUT模式 
   查找行尾– CR / LF约定
   在模式末尾要当心+和*
   调试
   例外情况
   Windows上的Pexpect
   API文档
   核心预期组件
   fdpexpect-将pexpect与文件描述符一起使用
   popen_spawn-将pexpect与管道子进程一起使用
   replwrap-控制读取评估打印循环
   pxssh-控制SSH会话
   例子
   常问问题
   常见问题
   线程数

send（）和sendline（）的计时问题
在孩子退出之前截断的输出
在Solaris上控制SSH
孩子没有收到完整的输入，发出BEL
历史
发行版
移动和叉子
