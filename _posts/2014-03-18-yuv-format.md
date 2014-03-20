---
layout: post
title: YUV格式
date: 2014-03-18 19:00:00
category: 学术
tags: [视频]
---
{% include JB/setup %}

YUV是一种表示视频像素颜色的格式。与它相比较的是RGB。RGB格式用红绿蓝三个颜色分量来表示一个像素，YUV则是用一个亮度分量（Y）加两个色度分量（U、V）来表示一个像素。

<!--more-->
YUV也经常被称为YCbCr。Cb为蓝色色度分量，对应U；Cr为红色色度分量，对应V。YUV格式根据三个分量数据存储方式的不同又分为几类。

##Planar的YUV
这是比较适合视频编码的一种格式。在这种格式下，三个分量是分别存储的，即分为三个plane（可对它们分别编码）。像下面这样（示例为4x4的图像，下同）：

    YYYYYYYYYYYYYYYY UUUU VVVV

这个例子所示的YUV常被称为YUV420p，其水平和竖直方向上均为每两个亮度分量才有一个色度分量，也就是每四个Y才需要一个U和V。除了这种水平和数值方向的色度分量都下采样的4:2:0，还有只有水平方向下采样的4:2:2格式，以及没有下采样的4：4：4格式。

上面的例子是按Y、U、V的顺序（YCbCr）存储的，如果改为Y、V、U的顺序（YCrCb），则称为YV12格式。

##Packed的YUV

Packed的YUV中亮度分量和色度分量是交叉存储的（这一点类似RGB格式）。这种YUV经常用在网络摄像机里，且大多都是4:2:2的，也就是只有在水平方向下采样。

例如：

    UYVYUYVYUYVYUYVYUYVYUYVYUYVYUYVY

##Semi-planar的YUV

这是Planar和Packed两种格式的混合体：Y分量单独一个plane，U和V则交叉存储。这种YUV一般都是4:2:0的。例如：

    YYYYYYYYYYYYYYYY UVUVUVUV

这个示例称为NV12格式。如果交换UV的顺序，就成了NV21格式。NV21是Android系统中[摄像头预览图片的默认格式](http://developer.android.com/reference/android/hardware/Camera.Parameters.html#setPreviewFormat(int))。
