---
layout: post
title: iOS和Android上的音频输出
date: 2014-04-16 17:00:00
category: 技术
tags: [iOS, Android, 编程]
---
{% include JB/setup %}

我在开发Android和iOS上的视频播放器。二者底层的解码和控制代码可以统一，但涉及音视频输出的部分在不同的系统上是有差别的。
对于视频输出，可以用同样的架构，即OpenGL；但对于音频输出，两个系统上的处理则完全不一样。

<!--more-->
在Android系统上，
