---
layout: post
title: 如何获取唯一的机器码
date: 2013-08-05 22:00:0
category: 技术
tags: [Linux, Windows, Programming]
---
{% include JB/setup %}

获取唯一的机器码是采用密钥对软件进行保护的基础。一般来说，一台电脑的主要部件都会有一个序列号或是其他唯一的ID。
唯一的机器码可以用这些ID组合变换得到。下面介绍在Windows下和在Linux下该如何做。

<!--more-->
####Windows
Windows系统提供了一个被称为[WMI](http://en.wikipedia.org/wiki/Windows_Management_Instrumentation)的机制，可用于管理各个部件。
我们可以借助它来想操作系统查询各个部件的属性。

####Linux
