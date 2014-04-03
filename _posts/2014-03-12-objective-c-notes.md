---
layout: post
title: Objective-C笔记（一）
date: 2014-03-12 21:44:09
category: 技术
tags: [编程, Objective-C]
---
{% include JB/setup %}

iOS编程使用的语言主要是Objective-C。Objective-C在C语言基础上增加了面向对象的特性和一些新的语法。
最近重新看了Apple开发者网站上的文档[《Programming with Objective-C》](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC)，本文是一些笔记。

<!--more-->

##定义类

类是对象的蓝本。类的接口（`interface`）定义了它所期望的交互。类的实现（`implementation`）提供内部行为。接口中的属性（`property`）用来访问对象的值，接口中的方法提供了对象可以接收和响应的消息。
用`+`和`-`来分别表示类方法（或静态方法）和实例方法。

Objective-C的类也是对象。它们是`Class`这个类的对象。

##操作对象

对象之间互相发送和接收消息。对象也可以给自己发消息，也就是类在实现时用`self`来调用自己的其他方法。

对象是动态生成的。一个类的`alloc`方法产生一个对象，即分配了一块内存来存储这个对象的值。然后这个对象可以调用`init`方法来对它的值进行初始化。
有很多`init`方法，提供不同参数的初始化方式。如果是无参数的初始化，那么`[[SomeClass alloc] init]`可以用`[SomeClass new]`来代替。

##数据的封装

属性封装了一个对象的值。编译器会为每个属性生成读和写的方法，可以用这些方法访问属性，也可以用点操作符。可以修改或重写这些方法。

编译器会为每个属性提供一个实例变量来储存它的值，该实例变量名是在属性名前面加一个下划线。可以修改这些实例变量名。

一般应该通过属性来访问这些实例变量，但在类的实例方法中，也可以直接使用这些示例变量。还可以额外定义实例变量而不提供对应的属性。这一般用来辅助类的实现。

##更多

关于对象之间的关系以及Objective-C里的一些高级特性，后面再写。






