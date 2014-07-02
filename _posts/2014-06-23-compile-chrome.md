---
layout: post
title: 编译Chrome浏览器
date: 2014-06-23 17:00:00
category: 技术
tags: [Chrome, Linux, VirtualBox]
---
{% include JB/setup %}

为了解[WebRTC](/posts/webrtc-introduction/)的实现，我按照Chromium项目[官网](https://code.google.com/p/chromium/)上的[说明](https://code.google.com/p/chromium/wiki/LinuxBuildInstructions)从源码编译了Chrome。

<!--more-->

这是一个庞大的项目。下载源码花了不少时间。安装依赖项也不是很顺利。上面链接的说明文档里介绍的每一步如果报错都会影响下一步的进行。我遇到的报错都是网络不给力（包括软件源不完整、连接不上等）造成的。后来干脆采用了VPN，就一切OK了。

为了编译成功，需要足够的磁盘和内存（具体数值参见说明文档）；为了不至于花太长时间，还需要足够快的处理器。我使用的是实验室台式机上的Ubuntu虚拟机。下面的截图反映了这一颇为壮观的过程。

![](/images/2014-06-23-compile-chrome-status-1.png)


![](/images/2014-06-23-compile-chrome-status-2.png)


第一个图反映出编译阶段，四核八线程的CPU以3.64GHz的频率满负荷运行的状况。第二个图则显示了链接阶段高达7G的内存占用。由于我给虚拟机分配的物理内存只有3G，因此Swap区域有4个G被使用。

在编译期间我中断过几次，主要是为调整虚拟机的配置。按照VirtualBox的提示，由于我的主机处理器只有四个物理核，虚拟机CPU核数设为4比设为8更好些。另外，之前我的虚拟磁盘存储空间是动态分配的，但这样边分配边编译非常慢，我把它改为了预分配好的固定大小的虚拟磁盘。
