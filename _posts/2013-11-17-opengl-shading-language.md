---
layout: post
title: OpenGL中的着色语言
date: 2013-11-17 13:00:0
category: 技术
tags: [Programming, OpenGL]
published: true
---
{% include JB/setup %}

之前曾介绍过[OpenGL的基本概念](http://blog.shengbin.me/posts/opengl-concepts/)。本文介绍[OpenGL中的着色语言（Shading Language）](http://www.opengl.org/documentation/glsl/)。

<!--more-->
##简介

着色语言（简称SL）主要是用来写OpenGL 2.0（及以上版本）中可编程流水线使用的程序。
这些程序被称为着色器（Shader），分为顶点着色器（Vetex Shader）和片段着色器（Fragment Shader）。
它们在GPU的处理单元中执行，来决定模型最终渲染出来的图像。
这两种着色器是成对出现的，GPU先执行前者，后执行后者。

##语法

SL的语法类似于C语言。我们结合例子进行说明。

以下是两个配对的顶点着色器和片段着色器代码。

{% highlight cpp %}
// Vertex Shader
attribute vec4 a_position;
attribute vec2 a_texCoord;
varying vec2 v_tc;
void main()
{
	gl_Position = a_position;
	v_tc = a_texCoord;
};
{% endhighlight %}

{% highlight cpp %}
// Fragment Shader
varying lowp vec2 v_tc;
uniform sampler2D u_texY;
uniform sampler2D u_texU;
uniform sampler2D u_texV;
void main()
{
	mediump vec3 yuv;
	lowp vec3 rgb;
	yuv.x = texture2D(u_texY, v_tc).r;
	yuv.y = texture2D(u_texU, v_tc).r - 0.5;
	yuv.z = texture2D(u_texV, v_tc).r - 0.5;
	rgb = mat3( 1,   1,   1,  
	            0,       -0.39465,  2.03211,
	            1.13983,   -0.58060,  0) * yuv;
	gl_FragColor = vec4(rgb, 1);
};
{% endhighlight %}

首先每个程序有一个main函数。在顶点着色器中，有个gl_开头的变量，`gl_Position`，这是SL的内置变量。
顶点着色器的目的就是给这个变量赋值，也就是给出每个顶点的最终位置。
在片段着色器中，也有一个类似的内置变量`gl_FragColor`。片段着色器主要是给这个变量赋值，也就是计算出渲染结果图像每个像素的颜色。

在SL中，除了内置变量外，其他变量可以分为四种。其中三种用于不同部件之间传递数据，包括属性值(attribute)、不变量(uniform)和渐变量(varying)；其余的是第四种，用于程序内存储中间结果。
每个变量可以指定精度修饰符（lowp，mediump，highp），不同精度值的范围也不一样。变量的类型除了void, int, bool, float等C语言中的类型，还有vec和mat类型用来表示向量和矩阵。
此外，还有个特殊类型Sampler2D。

关于SL语法更详细的介绍可以参见[这里](http://db-in.com/blog/2011/02/all-about-opengl-es-2-x-part-23/#shader_language)。

##数据传递

上文提到，SL中有三种变量用来传递数据：属性值(attribute)、不变量(uniform)和渐变量(varying)。其中属性值和不变量用于从CPU向GPU传送数据，而渐变量主要用于顶点着色器和片段着色器之间的传递。
下面分别介绍。

###属性值

属性值用于从CPU向顶点着色器传递数据。这些数据通常是顶点的属性，即需要由应用程序设置的值。
一个3D模型可能有很多个顶点，每个顶点有位置、颜色等属性。通常把这些属性存放在一个数组中，然后送给GPU的顶点着色器。
顶点着色器会对交给它的每个顶点的位置属性进行处理，例如完成模型物体的移动、翻转等操作，然后计算出每个顶点最终应该在的位置，赋给`gl_Position`。

属性值只能出现在顶点着色器中。

###不变量

如果有常量需要传给GPU，可以考虑用不变量。不变量的使用与属性值类似，其特殊之处在于：

1. 在顶点着色器和片段着色器中都可以用。

2. 对每个顶点处理单元和片段处理单元都是一样的值。

###渐变量

顶点的颜色属性也可以在顶点着色器里进行计算，但计算结果在这里并没有最终归宿；一般顶点着色器需要把该结果（或者干脆把原始颜色属性不加处理地）传给片段处理器，
在那里用来决定最终像素的颜色并向`gl_FragColor`赋值。这个从顶点着色器向片段着色器传递数据的任务就是由渐变量完成的。

与顶点颜色类似的还有顶点的纹理坐标。纹理坐标指明该顶点去二维纹理（OpenGL里的专门术语Texture2D）的哪个地方取纹理值。
纹理值影响的是渲染结果的像素颜色，所以纹理坐标归根结底也要在片段着色器里使用。
但应用程序里设置的纹理坐标值无法直接传给片段处理器，也需要先传给顶点着色器中属性值，然后以渐变量的形式传给片段着色器。

渐变量有一个重要的特点是可以自动插值。从CPU传给GPU的只是模型每个顶点的属性，对于顶点之间的线或面上的像素属性（主要是其颜色和纹理坐标），渐变量可以根据该像素在顶点间的位置自动插值出来。
这个特点也是为符合其用途的需要。

##着色程序的使用

SL源代码可以存储在单独的文件中，或者以字符串的形式写在OpenGL应用程序源代码中。
在OpenGL初始化时，有专门的API来编译、链接、使用着色器程序。

以上就是关于OpenGL着色语言的最重要的概念。掌握了这些，基本上就可以读写简单的着色程序了。最后，希望渲染顺利。