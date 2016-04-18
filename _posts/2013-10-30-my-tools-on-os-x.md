---
layout: post
title: 我在OS X上需要的工具
date: 2013-10-30 23:00:0
category: 技术
tags: [Mac, OS X, 开源]
---

我在OS X系统上需要使用不少命令行工具，它们被放在~/tools文件夹下。
以下是对这些工具的简单说明和相关链接。

<!--more-->

## adt-bundle

Android开发工具包，含有Eclipse和Android SDK。这年头的程序员，机器上没有这个就跟不上潮流了。

链接：<http://developer.android.com/sdk/index.html>

## android-ndk

Android底层开发工具包。顾名思义吧。

链接：<http://developer.android.com/tools/sdk/ndk/index.html>

## ffmpeg

著名的开源音视频库FFmpeg，包括常用的音视频处理程序ffmpeg和简易媒体播放器ffplay。
(从它分出来的[Libav](http://libav.org/)也在我的~/tools目录下，就不单独列出了，虽然相比FFmpeg我更喜欢Libav的命名规范。）

链接：<http://ffmpeg.org/>

## SDL

一个跨平台的多媒体中间件，打通应用程序和硬件的接口，用于播放器、游戏等。

链接：<http://www.libsdl.org/>

## x264

高效的H264/AVC编码器。

链接：<http://www.videolan.org/developers/x264.html>

## pkg-config

一个编译时使用的辅助工具，用来获取已安装的包或库的路径等信息。比如ffmpeg的编译系统使用它来定位x264、SDL等外部库的可用性。

链接：<http://www.freedesktop.org/wiki/Software/pkg-config/>


## autoconf, automake

这两个工具使得程序可以方便地从源码自动配置和编译，用于许多开源项目的发布（上面介绍的pkg-config就是一例）。其中automake依赖autoconf。

参见以下链接：  
autoconf: <http://www.gnu.org/software/autoconf/>  
automake: <http://www.gnu.org/software/automake/>

## libtool

主要是被上面介绍的autoconf和automake使用。本来OS X系统自带有libtool，但由于其版本较低（引发了一些问题），所以还是自己下一份最新代码来生成了。

链接：<http://www.gnu.org/software/libtool/>

## yasm

一个常用的x86汇编器，很多程序需要用它来生成代码。

链接：<http://yasm.tortall.net/>
