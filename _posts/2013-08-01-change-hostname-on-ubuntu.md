---
layout: post
title: Ubuntu系统下修改机器名称(hostname)
date: 2013-08-01 17:00:0
category: 技术
tags: [Linux]
---
{% include JB/setup %}

最近因为实验室工作的需要，又开始使用Ubuntu。安装系统的时候没注意选好机器名称(hostname)，导致后来想修改。

在Linux下用以下命令可查看当前主机名：
    hostname

<!--more-->
若想临时改为Shengbin-LabPC，可执行以下命令:
    sudo hostname Shengbin-LabPC
    
但这样只在当前登录下有效，重启之后又回到了原来的名称。要永久修改，执行以下命令：
    sudo vi /etc/hostname
将相应文件中的内容改为新的名称。

其他地方也存在旧名称，亦将其更新。例如：
    sudo vi /etc/hosts

