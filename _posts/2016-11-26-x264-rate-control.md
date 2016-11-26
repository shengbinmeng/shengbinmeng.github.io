---
title: x264码率控制
date: 2016-11-26 12:00:00
category: 技术
tags: [x264, H.264]
---

x264是业界著名的H.264/AVC视频编码器。本文介绍一下它的码率控制相关设置。

<!--more-->

码率控制是指视频编码中决定输出码率的过程。虽然下面的具体名称和参数是x264独有的，但所涉及的码率控制概念和模式却是对所有视频编码器都适用的。

## CQP （Constant QP）
	
`x264 --qp`

恒定QP（Quantization Parameter），追求量化失真的恒定，瞬时码率会随场景复杂度而波动。这种模式基本被淘汰（被下述CRF取代），只有用“--qp 0”来进行无损编码还有价值。

## CRF（Constant Rate Factor）

`x264 --crf`

恒定质量因子，与恒定QP类似，但追求主观感知到的质量恒定，瞬时码率也会随场景复杂度波动。
对于快速运动或细节丰富的场景会适当增大量化失真（因为人眼不易注意到），反之对于静止或平坦区域则减少量化失真。

## ABR（Average Bitrate）

`x264 --bitrate`

平均码率，追求整个文件的码率平均达到指定值（对于流媒体有何特殊之处？）。瞬时码率也会随着场景复杂度波动，但最终要受平均值的约束。

## CBR（Constant Bitrate）

恒定码率。前面几个模式都属于可变码率（瞬时码率在波动），即VBR（Variable Bitrate）；恒定码率与之相对，即码率保持不变。

x264并没有直接提供CBR这种模式，但可以通过在VBR模式的基础上做进一步限制来达到恒定码率的目标。CRF和ABR模式都能通过`--vbv-maxrate`、`--vbv-bufsize`来限制码率波动。

## 参考

<https://trac.ffmpeg.org/wiki/Encode/H.264>：

> There are two rate control modes that are usually suggested for general use: Constant Rate Factor (CRF) or Two-Pass ABR.


<http://slhck.info/articles/crf>：

> The Constant Rate Factor (CRF) is the default quality setting for the x264 encoder.


<https://en.wikibooks.org/wiki/MeGUI/x264_Settings>：

> It is generally not recommended to use bitrate mode without 2pass encoding.


<http://stackoverflow.com/questions/12221569/x264-rate-control>：

> If you are in low delay video conferencing mode for minimal jitter and reliable performance you need to be in CBR mode and not VBR mode. It is the VBR mode that gives the shoot ups and peaks that a live low latency system cannot deal with.
  There are specialized vbr algorithms for low latency video communication / surviellance applications but x264 does not have that. So don't use its vbr. its vbr is meant for storage.