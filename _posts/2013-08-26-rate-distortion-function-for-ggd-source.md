---
layout: post
title: "广义高斯分布信源的率失真函数"
date: 2013-08-26 17:30:01
category: 学术
tags: [信源编码, GGD, 率失真, 建模]
---

论文[《Statistical Model, Analysis and Approximation of Rate-Distortion Function in MPEG-4 FGS Videos》](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=1626284&tag=1)
结合了分析型的方法和经验型的方法对广义高斯分布（GGD）信源的率失真函数进行了建模。

<!--more-->
广义高斯分布信源的率失真函数无法得到显式的表达式。论文试图从率失真函数的导数入手进行分析。

经过公式推导，先得出导数与beta参数无关，这样分析其性质时就不需要考虑这个因素的影响。

然后只考虑参数alpha，画出不同参数值下的率失真函数导数的曲线，观察其性质，发现均先减小后增大至6.02。

实际编码的码率较低，都落在前半部分持续减小的范围内。因此就认为导数递减至常数a。据此来建立RD函数模型如下：

    D(R) = a*R + C + [-k/(1+b*R)]

其中的a, b, k, C都是模型参数。根据实际数据，可以得到这些参数的经验值。

最后验证该模型的有效性。
