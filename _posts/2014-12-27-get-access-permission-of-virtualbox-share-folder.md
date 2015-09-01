---
layout: post
title: 获取VirtualBox共享文件夹的访问权限
date: 2014-12-27 12:00:00
category: 技术
tags: [Linux, VirtualBox]
---

使用VirtualBox虚拟机时，宿主机和虚拟机之间交换文件最方便的方式就是共享文件夹了。

<!--more-->

例如，选择宿主机的Desktop做共享，设置为自动mount，然后每次虚拟机开机后就可以看到这个文件夹。
在虚拟机是Ubuntu系统的情况下，这个文件夹被装载为/media/sf_Desktop。

但想要访问这个文件夹的时候，却会出现权限不足的问题。原因在于：系统自动mount的文件夹所有者为root，所属组为vboxsf，且只向这两者开放访问权限。

于是，要想获取VirtualBox共享文件夹的访问权限，最有效的解决方法是：把当前用户加入vboxsf组。命令如下：

	$sudo usermod -aG vboxsf $(whoami)
	
在执行完这个命令后，并不能立即生效，需要一次重新登录。对于Linux用户来说，重新登录（或者重启）简直是不可接受的，
所以[有人指出](http://askubuntu.com/questions/69221/adding-user-to-a-group-why-had-to-reboot)，
执行完上述命令后也可以通过执行`newgrp vboxsf`来代替重新登录。