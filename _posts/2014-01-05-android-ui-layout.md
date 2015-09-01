---
layout: post
title: Android的UI布局
date: 2014-01-05 17:00:00
category: 技术
tags: [Android, 编程]
---

本文总结一下Android系统中用户界面的布局，主要面向开发者。不少人——例如我——可能搞了挺久的Android开发都不一定对这些基本概念有深刻的认识。
仔细读一读官方的指南（<http://developer.android.com/guide/topics/ui/index.html>），还是受益匪浅的。

<!--more-->
Android的UI布局由视图（View）和视图组（ViewGroup）构成。视图是屏幕上一块绘图区域；视图组是则是一个不可见的容器。视图组中包含一到多个其他视图。
视图组中也可以再包含视图组，形成层次化的结构。从代码上说，视图和视图组对应View和ViewGroup这两个Java类，其中后者是前者的子类（我个人觉得从意义上二者并不构成父子关系，Android系统这样处理可能是因为它们有共同属性吧）。

建造UI布局既可以在代码里new出来这些类的实例，也可以通过XML的方式定义。Android系统在加载布局文件时将其中的XML节点转化为相应类的实例。

下面是一个XML布局文件示例。
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
			  android:layout_width="fill_parent" 
			  android:layout_height="fill_parent" 
			  android:orientation="vertical" >
	<TextView android:id="@+id/text"
			  android:layout_width="wrap_content"
			  android:layout_height="wrap_content"
			  android:text="Hello, I am a TextView" />
	<Button android:id="@+id/button"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:text="Hello, I am a Button" />
</LinearLayout>
{% endhighlight %}

最外层的LinerLayout是ViewGroup的子类。这个视图组包含了两个视图，TextView和Button，它们都是View的子类。

每个布局元素（视图或视图组）有一些属性，属性值可以在XML文件中指定或者在代码中修改。上面所示的XML指定了诸如`id`、`text`、`orientation`，和以`layout_`开头的一些属性。

每个元素可能有自己独特的属性。需要特别注意的是以`layout_`开头的那些属性。这些是与布局有关的属性。
在代码中，这些属性用LayoutParams类描述，每个布局元素都有一个该类的实例。

每个ViewGroup类中都嵌套定义了一个自己专有的LayoutParams子类。这个ViewGroup中包含的布局元素就需要实例化该ViewGroup所定义的LayoutParams子类。
上面的例子中，TextView中的LayoutParams实例其实是LinearLayout.LayoutParams的实例。
之所以这样是因为不同的ViewGroup对其包含的布局元素的要求是不同的，也就是需要它们提供不同的布局参数（LayoutParams）。

有些属性是每个LayoutParams都会有的，比如`layout_width`和`layout_height`。
有些属性是某些ViewGroup的LayoutParams才有的，例如`layout_gravit`和`layout_marginLeft`。

只有理解了这些布局相关的概念和它们之间的关系，才能在设计界面时得心应手。