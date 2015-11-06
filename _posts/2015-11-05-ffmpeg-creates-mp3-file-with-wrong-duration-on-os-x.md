---
layout: post
title: FFmpeg生成的MP3文件在OS X上时长不对
date: 2015-11-05 12:00:00
category: 技术
tags: [OS X, FFmpeg]
---

今天在将OGG格式的音乐文件转为MP3格式时，发现FFmpeg生成的MP3文件在OS X的Finder和iTunes里都显示错误的时长。
如果你也遇到了这个问题，可以采用下面这两种方法解决。

<!--more-->

[第一种方法](https://trac.ffmpeg.org/ticket/2697)是加上`-write_xing 0`参数：

	ffmpeg -i song.ogg -write_xing 0 song.mp3

[第二种方法](http://unix.stackexchange.com/questions/97479/avconv-creates-creates-files-whose-duration-is-incorrect)是改用管道方式输出到文件：
	
	ffmpeg -i song.ogg -f mp3 -> song.mp3
	
第二种方法的参考网页里是针对`avconv`的，不过`avconv`和`ffmpeg`用法基本是一样的。

至于这个问题的原因，目前还不清楚。FFmpeg编码MP3用的是[LAME](http://lame.sourceforge.net/)，但我试了试发现`lame`命令并没有问题。
网上[有人暗示](http://askubuntu.com/questions/441052/incorrect-duration-time-in-audacious-or-any-musical-player-when-convert-with-ffm/677029#677029)是FFmpeg在写文件头的时候出错了，
但我并没能确认。我用的FFmpeg版本如下：

	ffmpeg version 2.8 Copyright (c) 2000-2015 the FFmpeg developers
	built with Apple LLVM version 7.0.0 (clang-700.0.72)
	configuration: --prefix=/usr/local/Cellar/ffmpeg/2.8 --enable-shared --enable-pthreads --enable-gpl --enable-version3 --enable-hardcoded-tables --enable-avresample --cc=clang --host-cflags= --host-ldflags= --enable-opencl --enable-libx264 --enable-libmp3lame --enable-libvo-aacenc --enable-libxvid --enable-vda
	libavutil      54. 31.100 / 54. 31.100
	libavcodec     56. 60.100 / 56. 60.100
	libavformat    56. 40.101 / 56. 40.101
	libavdevice    56.  4.100 / 56.  4.100
	libavfilter     5. 40.101 /  5. 40.101
	libavresample   2.  1.  0 /  2.  1.  0
	libswscale      3.  1.101 /  3.  1.101
	libswresample   1.  2.101 /  1.  2.101
	libpostproc    53.  3.100 / 53.  3.100

也不知道最新的版本是否还存在这个问题。

本文的主要目的是提供解决方法。我自己试了试两种方法都可以得到正确的结果，任选其一即可。

###Bonus

我这次是要批量转码一堆文件，采用的命令是：

	for name in *.ogg; do ffmpeg -i "$name" -write_xing 0 "${name/.ogg/.mp3}"; done;