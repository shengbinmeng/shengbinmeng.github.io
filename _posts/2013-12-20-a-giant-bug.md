---
layout: post
title: 可以咬坏你系统的大虫子
date: 2013-12-20 17:00:00
category: 技术
tags: [Linux, 编程, 趣]
---

标题是为了哗众取宠。本文实际要讲的是某程序中的一个巨大bug，它足以损坏你的操作系统。嗯，bug原意就是虫子，所以其实标题也没什么错。

<!--more-->
关于这个GIANT BUG的原始资料在这里：<https://github.com/MrMEEE/bumblebee-Old-and-abbandoned/commit/a047be85247755cdbe0acce6f1dafc8beb84f2ac#L1L351>。
读者诸君莫要错过！

简单来说，在一个Linux程序的安装脚本里，原本应该是这样的一句代码：

    rm -rf /usr/lib/nvidia-current/xorg/xorg

被写成了这样：

    rm -rf /usr/ lib/nvidia-current/xorg/xorg
    
差别不过一个空格，但结果却大不相同。

首先，`rm -rf`是个很危险的命令，可以在不给用户任何提示的情况下递归(`-r`)、强制(`-f`)的删除指定文件夹下的所有数据。

其次，程序的安装脚本是在root权限下执行的，因此不会有任何障碍。

最后，也是产生破坏性的一点，`/usr/`是一个重要的系统文件夹，删了就只能后悔莫及了。

于是，上面的代码本来是要在不烦扰用户的情况下删除位于`/usr/lib/nvidia-current/xorg/xorg`的（已经没用的）文件，
却由于多写了一个空格，变成了删除系统目录`/usr/`下的所有数据！

![悲剧啊！](/images/2013-12-20-what-a-tragedy.jpg)

在本文开始给出的链接页面上，关于这个bug有很多吐槽，其中不乏欢乐的图片。上面只是其中一张中文的，其他的自行去围观吧。
