---
layout: post
title: 解决由Hyper-V导致的VirtualBox虚拟机无法运行问题
date: 2013-10-15 20:00:0
category: 技术
tags: [Windows, VirtualBox]
---
{% include JB/setup %}

最近在我的台式机上安装了Windows Phone SDK 8.0，然后打开VirtualBox创建的64位Ubuntu虚拟机时，出现了如下错误提示：

    VT-x/AMD-V hardware acceleration has been enabled, but is not operational. Your 64-bit guest will fail to detect a 64-bit CPU and will not be able to boot.
	Please ensure that you have enabled VT-x/AMD-V properly in the BIOS of your host computer.

<!--more-->
经过一番探索，发现这是因为Windows Phone SDK 8.0在安装时打开了Hyper-V导致的。Windows Phone SDK 8.0中的手机模拟器需要用Hyper-V来实现虚拟化。

[Hyper-V](http://baike.baidu.com/view/1359943.htm)是微软的虚拟化软件，类似VirtualBox，可以用来创建虚拟机。它们被称为[Hypervisor](http://en.wikipedia.org/wiki/Hypervisor)或Virtual Machine Monitor（虚拟机监视器）。
由于Hyper-V与VirtualBox不能共存，因此当Hyper-V打开时，VirtualBox就不能正常工作了。上面错误提示中提到的”VT-x/AMD-V“是处理器的硬件虚拟化支持，已经在BIOS中打开了；
Hyper-V使用了它，于是VirtualBox就抱怨说无法使用（”not operational“）了。

解决方法就是禁用Hyper-V。途径有多种：

1. 使用管理员权限运行命令：`bcdedit /set hypervisorlaunchtype off`。对应的打开Hyper-V的命令是`bcdedit /set hypervisorlaunchtype auto`。
2. 使用”添加或删除Windows组件“图形界面程序，在里面取消勾选Hyper-V即可。

上述方法都需要重启计算机。而且禁用Hyper-V之后就无法使用Windows Phone SDK中的手机模拟器了。目前还没有找到VirtualBox虚拟机和Hyper-V同时运行的方法，除非VirtualBox中虚拟机是32位的
（我试了32位Windows XP的虚拟机在启用Hyper-V时仍可以正常运行）。
