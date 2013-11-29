---
layout: post
title: OpenGL中的着色语言
date: 2013-11-17 13:00:0
category: 技术
tags: [Programming, OpenGL]
published: false
---
{% include JB/setup %}

之前曾介绍过[OpenGL的基本概念](/posts/opengl-concepts/)。本文介绍OpenGL中的着色语言（Shading Language）。

<!--more-->
##简介

着色语言（简称SL）主要是用来写OpenGL 2.0（及以上版本）中可编程流水线使用的程序。
这些程序被称为着色器（Shader），分为顶点着色器（Vetex Shader）和片段着色器（Fragment Shader）。
它们在GPU的处理单元中执行，来影响模型最终渲染出来的图像。
这两种着色器是成对出现的，GPU先执行前者，后执行后者。

##语法

SL的语法类似于C语言。