---
layout: post
title: 在Android上运行命令行程序
date: 2014-12-29 12:00:00
category: 技术
tags: [Linux, Android, Shell]
---
{% include JB/setup %}

Android的基础是Linux，所以也能像Linux那样运行命令行程序。本文主要介绍一些需要注意的地方和可能遇到的问题。

<!--more-->

首先，这样的程序必须是由为Android打造的特定工具链编译出来的。Google官方提供的[NDK](https://developer.android.com/tools/sdk/ndk/index.html)
（原生开发包）中提供了这样的工具链。一般用它来进行交叉编译即可。

其次，编译出来的程序要拷贝到Android的可执行文件系统中。一般的Android设备其SD卡都是被mount为不可执行的，也就是说在这上面只能存储数据，无法运行程序。
所以我们要将程序拷贝到SD卡以外的地方，例如/data下面的文件夹。要操作SD卡以外的文件系统，需要root权限，所以要求设备必须是已经root过的。

对于开发者来说，传输文件最方便的就是adb push了。而adb push只有shell用户权限，所以目标文件夹必须对shell用户可写。
初始情况下，只有SD卡（对应文件系统中的/sdcard）对shell用户可写，因此需要采取下面两种方法之一来确保没有拒绝访问的问题：

1. 先adb push把文件传到/sdcard，然后adb shell转到设备上，在设备上用root用户把文件从/sdcard拷贝到/data。

2. 先adb shell转到设备上，用root用户在/data下创建一个shell用户（或者干脆所有用户）可读写的文件夹，然后用adb push把文件传输到这个文件夹。

最后，程序必须以root用户执行。其实完成上一步时，你应该已经是root用户了。开始享用把Android当作普通Linux的乐趣吧！

由于Android对Linux有所删减，所以有些命令是不能用的。例如sudo就不可用，所以在上述操作中要用root权限只能直接su到root了。