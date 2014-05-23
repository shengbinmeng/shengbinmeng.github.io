---
layout: post
title: 用OpenAL在iOS上输出音频
date: 2014-05-23 19:00:00
category: 技术
tags: [iOS, OpenAL]
---
{% include JB/setup %}

我在[《iOS和Android上的音频输出》](/posts/audio-output-on-ios-and-android/)这篇文章中提到，如果用系统提供的类来输出音频，只能是拉的模式。
但如果希望像Android上的AudioTrack那样用推的方式输出音频，可以采用[OpenAL](http://en.wikipedia.org/wiki/OpenAL)。

<!--more-->
OpenAL与OpenGL是同一个组织提出的标准，两者的目的、机制，甚至API都非常相似。首先，初始化一个渲染环境，包括配置设备、生成缓冲区，确定参数，等等。
然后就可以向缓冲区写入数据并调用渲染了。

如果是只输出一段固定的PCM数据，会简单一些，只需要一个缓冲区来存储这些数据；但如果是输出流式的音频，比如媒体播放的时候，就需要一个缓冲区队列。
网上有一些[资](http://devmaster.net/posts/2895/openal-lesson-8-oggvorbis-streaming-using-the-source-queue)[料](http://benbritten.com/2010/05/04/streaming-in-openal/)介绍了如何在iOS上实现流式播放，可以参考。

所谓推的方式，就是我们可以随时向缓冲区写数据。但如果写的过快，缓冲区队列会被填满。这时候写函数只能被阻塞。
OpenAL提供的API可以查询空闲的缓冲区数目。于是我们在写函数中就先检测是否有空闲缓冲区，有的话就写入，否则就等待。

OpenAL的缓冲区数目和每个缓冲区的大小都是可以设置的。这样我们就能根据需要来调整缓冲的总数据量。
由于我的程序中要用声音播放时间来进行音视频同步，所以不希望缓冲太多数据，就将缓冲区设的比较小。

作为各个平台通用的标准API，OpenAL还有一个优势就是能使程序可重用。在iOS上实现的代码，将来用于别的地方也只需要很少的改动。