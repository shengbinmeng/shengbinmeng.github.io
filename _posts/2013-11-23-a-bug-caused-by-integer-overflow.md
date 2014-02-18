---
layout: post
title: 整数类型溢出导致的一个bug
date: 2013-11-24 17:00:00
category: 技术
tags: [编程, C/C++]
---
{% include JB/setup %}

你能看出下面这段代码有什么问题吗？

{% highlight cpp %}
struct timeval timeNow;
gettimeofday(&timeNow, NULL);
int64_t timePassed = (timeNow.tv_sec - timeStart.tv_sec)*1000000 + (timeNow.tv_usec - timeStart.tv_usec);
int64_t delay = videoFrame->pts - timePassed;
if (delay > 0) {
    usleep(delay);
}
{% endhighlight %}

<!--more-->
这是一段用来显示视频的代码，其中隐藏的bug会导致视频画面卡死。在揭示bug之前，先理解一下它做了什么：
计算出从视频开始播放到现在的时间间隔（`timePassed`），把这个时间与当前视频帧的时间戳（`videoFrame->pts`）比较；
如果该时间戳大于从开始播放到现在的时间间隔，说明还没到显示这一帧的时候，就等待一会儿（`usleep(delay)`）。

实际执行的时候，视频能正常播放半个小时左右的时间，然后就画面就卡死了。后来发现卡死的原因竟然是程序在长时间的sleep！
也就是说，`usleep(delay);`这一句里的`delay`成为了一个巨大的值。为什么会这样的？

我们看到，`timePassed`的单位是微妙，用`int64_t`表示应该是足够的。
`struct timeval`的`tv_sec`域的类型长度是机器字长，在32位机上是`int32_t`型的，于是`timeNow.tv_sec - timeStart.tv_sec`也是`int32_t`型的。
这个差值是从开始播放到现在经过的秒数，用32位整数表示也是够的。但问题在于，当它与1000000相乘的时候，
得到的结果用32位整数表示就不够了。`int32_t`能表示的最大的正整数为[2,147,483,647](http://stackoverflow.com/questions/94591/what-is-the-maximum-value-for-a-int32)。
这样当播放的秒数达到2147以上时，`(timeNow.tv_sec - timeStart.tv_sec)*1000000`就超过了上述值。虽然赋值号左边赋值的对象是`int64_t`的，
但编译器在计算赋值号右边的值的时候并不会把`int32_t`提升至`int64_t`，在溢出时就将结果当成了一个接近-2,147,483,647的负值，并将这个负值赋给了`timePassed`。
于是`delay = videoFrame->pts - timePassed`就成为了一个巨大的正值，导致程序开始了长时间的睡眠。

出现这个bug主要是想当然地认为编译器会按照你赋给的变量的类型进行提升，但这种想法是错误的。赋值号右边在计算时是不考虑其左边的类型的，要想进行类型提升，必须显式指定。
将计算`timePassed`的代码改为如下这样就可以了：

{% highlight cpp %}
int64_t timePassed = ((int64_t)(timeNow.tv_sec - timeStart.tv_sec))*1000000 + (timeNow.tv_usec - timeStart.tv_usec);
{% endhighlight %}
