---
layout: post
title: FFmpeg中的时间戳（DTS和PTS）
date: 2014-01-10 17:00:00
category: 技术
tags: [Programming, FFmpeg]
---
{% include JB/setup %}

最近在开发基于FFmpeg的播放器，对FFmpeg里的时间戳（time stamp）做了较多的了解。下面是总结。

<!--more-->
FFmpeg里有两种时间戳：DTS（Decoding Time Stamp）和PTS（Presentation Time Stamp）。顾名思义，前者是解码的时间，后者是显示的时间。要仔细理解这两个概念，需要先了解FFmpeg中的packet和frame的概念。

FFmpeg中的用AVPacket结构体来描述解码前的数据包，用AVFrame结构体来描述解码后的一帧数据。对于视频来说，AVFrame就是视频的一帧图像。这帧图像什么时候显示给用户，就取决于它的PTS。DTS是AVPacket里的一个成员，
表示这个数据包应该什么时候被解码。如果视频里各帧的编码是按输入顺序（也就是显示顺序）依次进行的，那么解码和显示时间应该是一致的。可事实上，在大多数编解码标准（如H.264或HEVC）中，。