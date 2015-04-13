---
layout: post
title: Shell的类型
date: 2015-02-10 12:00:00
category: 技术
tags: [Linux, Shell, 编程]
---
{% include JB/setup %}

Shell是Linux上的“居家旅行必备品”。它既是在终端里用命令行进行各种操作的工具，也是一种用来写自动化操作脚本的编程语言。
作为一个工具程序，它的运行实例根据调用方式的不同可以分为不同的种类。

<!--more-->

##登录shell与非登录shell

Shell可以分为登录shell（login shell）和非登录shell（non-login shell）。

顾名思义，登录shell是需要用户登录的shell。登录就是指在调用shell时需要输入密码的情形。这包括：通过命令行界面或SSH登录系统、通过`su`来切换用户，等等。
而非登录shell则是在如下情形时调用的shell：进入图形界面系统后启动终端（Terminal）程序时、在一个已有的会话中启动另一个shell时，运行shell脚本时，等等（这些时候都不需要输入密码进行登录）。

不过上面这些只是表象。根据[bash的手册](http://linux.die.net/man/1/bash)，真正定义登录shell的应该是调用时的参数0（也就是程序名）是否是以`-`起始，或者是调用时有`--login`选项。
例如，通过命令行界面或SSH登录系统时之所以是登录shell，是因为此时调用的程序其实是`-bash`（以`-`起始的）。这可以从`ps -f`命令输出结果中的进程名称看出来：

	Shengbin-MacBook:~ shengbin$ ssh 172.31.203.138
	shengbin@172.31.203.138's password: 
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.13.0-48-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	Last login: Mon Apr 13 17:21:40 2015 from shengbin-macbook.local
	shengbin@Shengbin-VirtualBox:~$ ps -f
	UID        PID  PPID  C STIME TTY          TIME CMD
	shengbin  4458  4457  2 17:23 pts/25   00:00:00 -bash
	shengbin  4474  4458  0 17:23 pts/25   00:00:00 ps -f

当你在已有会话里重新开一个shell或者运行脚本时，若希望调用一个登录shell，可以使用`--login`选项。

## 交互shell与非交互shell

Shell还可以分为交互shell（interactive shell）和非交互shell（non-interactive shell）。前者会从标准输入向用户询问命令，并从标准输出将结果显示给用户，在命令行界面与用户交互的就是这种shell。
非交互的shell最常见的运行脚本时的shell，这种shell不通过键盘与用户交互。

## 不同类型shell的影响

对用户来说，shell的分类带来的最大影响莫过于启动时的配置了。以bash为例，如果它作为登录shell运行，例如用户登录系统时，则会首先读取并执行/etc/profile里的命令。
接下来，它按下面的顺序寻找并读取启动配置文件：~/.bash\_profile、~/.bash\_login、~/.profile，遇到第一个存在且可读的就不再继续了。在bash作为登录shell退出时，
它还会读取并执行~/.bash\_logout和/etc/bash.bash\_logout中的内容。反之，如果bash以非登录shell运行，例如用户启动终端程序或运行脚本时，它就不会读取上述那些文件。

非登录shell读取哪些配置文件还取决于它交互的还是非交互的。启动一个交互终端时，bash会读取并执行它的运行控制（run control）文件，默认为~/.bashrc。
而运行脚本时由于是非交互的，上面那些文件都不会起作用，bash会转而读取并执行环境变量BASH_ENV指示的文件。

Bash之外的其他shell，具体执行细节可能有所差异，但基本符合一些大原则：登录shell会读取带profile、login关键字的配置文件，非登录的交互shell会读取带rc关键字的运行控制文件，
非登录的非交互shell只会根据env关键字的变量来进行初始化。

在使用shell的过程中，有时会奇怪为啥自己增加的启动代码没有起作用。这很可能是因为这些代码写在了一个当前shell实例根本不会去读取的文件里。
知道了shell的种类和不同种类shell的影响，就可以有的放矢地配置和使用shell了。


