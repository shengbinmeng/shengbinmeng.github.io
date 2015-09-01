---
layout: post
title: 用FFmpeg录像时多线程写文件要加互斥锁
date: 2014-02-19 17:00:00
category: 技术
tags: [编程, FFmpeg, 多线程, 录像, Android]
---

最近在开发基于FFmpeg的Android手机录像软件。程序写好之后测试发现：录制音视频混合的文件总是出错，而关掉声音只录视频则没问题。
经过调试，原来是多线程导致的。

<!--more-->
FFmpeg录像分为音频的编码、视频的编码、音视频码流数据的封装这几个模块。
采集得到原始音视频后，分别进行编码，然后由FFmpeg提供的复用器将编码后数据封装成文件。

Android系统的视频采集和音频采集是在不同线程进行的。在各自的线程中，我先将采集到的音视频数据填充至FFmpeg所用的`AVFrame`结构体；
然后调用`avcodec_encode_video`或`avcodec_encode_audio`，将`AVFrame`中的数据进行编码得到`AVPacket`；
接着调用`av_interleaved_write_frame`将`AVPacket`中的码流写入文件。

问题就出在上述做法的最后一步！由于写入文件时操作的是同一个`AVFormatContext`，两个线程一起写就导致了访问冲突，不出错只能是运气了。

解决方法也很简单，只需将调用`av_interleaved_write_frame`的代码用互斥锁锁起来即可。这其实是多线程编程常见的问题，只是容易忽视罢了。

为什么FFmpeg不在内部做这个互斥工作呢？因为使用它的人可能不用多线程，这样的话调用`av_interleaved_write_frame`就在一个线程里，
就没必要增加额外的开销来加锁。所以还是让程序员根据实际情况去考虑互斥的事情吧。
