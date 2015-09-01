---
layout: post
title: 守护进程的子进程
date: 2014-06-11 17:00:00
category: 技术
tags: [Linux, 编程]
---

Linux/Unix上的后台服务程序称为守护进程（daemon）。守护进程与其他进程的主要区别是它不与用户交互，因此也就与输入输出没有关系。
今天在调试一个问题时，我们发现守护进程对其子进程的处理也有其特殊之处。

<!--more-->

我们的程序在运行过程中，需要使用`popen`来执行一个Shell命令。`popen`会打开一个管道、创建（`fork`）一个子进程并通过这个管道与子进程通信。
`popen`返回值是一个文件指针，通过它可以访问子进程的输出流（我们指定了只读），即用`fgets`来读取Shell命令的执行结果。读取完毕后，我们调用`pclose`关闭上述文件。

上面的程序在前台运行时没有任何问题，但在作为守护进程运行时就会出错。错误之处在于`pclose`失败。为什么会这样呢？

我们发现这跟两种情况下对子进程的不同处理有关。在Linux上，子进程结束执行时（也就是`exit`时），操作系统会向其父进程发送`SIGCHLD`信号。
一般的程序会对这个信号量采取默认处理，也就是保持其处理函数为`SIG_DFL`不变；但守护进程比较特殊，它对这个信号量的处理做了更改，也就是把它的处理函数设为了`SIG_IGN`。
更改的后果会导致`wait`调用失败，如[手册](http://man7.org/linux/man-pages/man2/wait.2.html)中所说：

> POSIX.1-2001 specifies that if the disposition of SIGCHLD is set to
SIG_IGN or the SA_NOCLDWAIT flag is set for SIGCHLD (see
sigaction(2)), then children that terminate do not become zombies and
a call to wait() or waitpid() will block until all children have
terminated, and then fail with errno set to ECHILD.

`wait`是父进程用来等待子进程结束并回收其资源的系统调用。`pclose`中会调用`wait`。由于后者失败了，所以`pclose`也失败。而且我们发现`errno`确实被设置为了“10，ECHILD”。

我们的程序在检测到`pclose`失败时会直接报错。但分析以上原因后会发现，在守护进程情况下`wait`调用失败是一个规定的行为，并不会产生负面后果（子进程已成功结束执行，而且没有成为僵尸）。
因此，我们可以在`pclose`失败后查看一下`errno`，如果是10就可以忽略这个失败，继续往下执行。话是这么说没错，但硬生生地忽略一个错误毕竟让程序员感到不爽。于是，最终我们采用了如下的解决方法。

解决方法：将守护进程对`SIGCHLD`的处理函数设为`SIG_DFL`，并保存旧的处理函数，等执行玩`pclose`之后再设置回去。

关于Linux上的进程与子进程的关系，等有时间了再专门写一篇介绍。
