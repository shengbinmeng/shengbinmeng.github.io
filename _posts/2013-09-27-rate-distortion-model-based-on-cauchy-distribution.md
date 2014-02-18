---
layout: post
title: "基于柯西分布的率失真模型"
date: 2013-09-27 16:30:01
category: 学术
tags: [新源编码, 率失真, 建模, Cauchy]
---
{% include JB/setup %}

<script type="text/javascript"
    src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

大部分率失真建模的论文都是假设信源符合拉普拉斯分布（也就是广义高斯分布的一种）。
论文[《Frame Bit Allocation for the H.264AVC Video Codec Via Cauchy-Density-Based Rate and Distortion Models》](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=1490553&tag=1)
则提出了一种基于柯西分布的率失真模型。

<!--more-->
作者观察发现，DCT变换的系数（尤其对于帧内编码的情况）用柯西分布能更好的拟合；但单纯的拟合准确度意义并不大，关键还是基于此分布假设的率失真模型更符合实际编码器输出。

根据柯西分布的数学表达式以及量化方法，可以推导出量化后数据的熵与量化步长Q的关系式H(Q)，以此来估计码率。这个表达式非常复杂，但根据其形状判断大致是对数曲线，
于是提出一个简化的式子来近似：
\\\[ H(Q) \approx a{Q^{ - \alpha }}, \\\]

其中\\( a \\)和\\( \alpha \\)都是参数，可由数据拟合出来。

类似的，失真与量化步长的关系也是先推导出复杂的表达式，然后用如下的简单式子进行近似：

\\\[ D(Q) \approx b{Q^{ - \beta }}. \\\]

用上面两个式子进行估计码率和失真的估计，与实际编码结果相符，则证明模型有效。将二者结合起来，可以得出失真D与码率R的关系：

\\\[ D = c{R^\gamma }, \\\]

其中\\( c = b/({a^{\alpha \beta }}), \gamma  = \alpha \beta \\)。

最终将上述率失真模型用于码率控制。码率控制算法目的是将一定量的比特数分配给若干帧，使得每一帧的画面质量（也就是失真）大致一样。
有了R与D的准确关系，自然会取得很好的控制效果，从而得到很平滑的画面质量。
