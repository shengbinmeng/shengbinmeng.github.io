---
layout: post
title: "HEVC中的块划分结构"
date: 2013-10-21 16:30:01
category: 学术
tags: [HEVC]
---
{% include JB/setup %}

在新一代视频编码标准[HEVC](http://en.wikipedia.org/wiki/High_Efficiency_Video_Coding)中，为了适应大分辨率视频并提供更灵活的编码块选取，引入了基于四叉树的分块结构。下面是其中一些重要概念。

<!--more-->
CTU（Coding Tree Unit）：类似于H.264里的MB；可以在SPS中指定CTU大小的上下限。一个视频帧，更准确说是一个视频片（Slice），将被划分为许多个CTU来处理。每一个CTU都将按照一棵四叉树来进行分块。

CU（Coding Unit）：CTU可以选择进一步划分或不划分。当CTU不再划分时，它就成了CU；当CTU按照四叉树形式划分时，四叉树的叶子节点就是一个个的CU。
CU是进行编码的基本单元，编码过程就是按照从左上到右下的顺序依次处理每个CU。每一个CU选择采用帧内预测模式还是帧间预测模式。

PU（Prediction Unit）：对于帧内预测的CU，有两种划分方式：直接作为一个PU，或按2x2方式划分为四个PU；对于帧间预测的CU，则有8种划分方式，将其划分为不同大小和形状的PU。

TU（Transform Unit）：以CU为根构造一个残差块的四叉树，这棵树的叶子节点就是一个个的TU。对于帧间预测的CU来说，一个TU可能包含多个PU，也就是说HEVC中多个PU预测的残差可以放在一起进行变换。

这样的划分机制十分灵活，允许编码器针对不同分辨率视频来选取适合的块大小，从而在编码效率和复杂度之间选择良好的折中。
同时，基于四叉树的设计又为不同大小的块提供了一致的表示方法，使得很多处理可以递归实现。
HEVC能够取得比H.264好一倍的压缩效果很大程度上得益于新的块划分结构。

更多信息可参见论文[《Block Partitioning Structure in the HEVC Standard》](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=6324412)。
