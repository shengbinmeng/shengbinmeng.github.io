---
layout: post
title:  "在Windows下使用Notepad++编写Markdown文件"
date:   2013-07-15 12:34:56
categories: 技术
tags: [Markdown, Windows]
---

Markdown文件本身是纯文本文件，可以用几乎任何文本编辑器编写。我在Windows下习惯用Notepad++来编辑文本文件。
现在既然决定用Jekyll发布博客，编写Markdown文件便成为经常需要做的事情。这篇博客是第一个自建的md文件，在Windows系统下用Notepad++写成。
这里主要说一下语法高亮的问题。

<!--more-->
Notepad++内置对多种语言的语法高亮支持，却不支持md语言。虽然没有语法高亮也可以写，但有的话毕竟方便点。很多厉害的文本编辑器都是可扩展的，
Notepad++也不例外。我们可以通过"Language"->"Define your language..."菜单来自定义对某种语言的格式支持。我大致看了一下在这个自定义窗口做的工作，
基本上就是为某些关键词和特定的符号指定相应的样式。你可以根据自己的喜好来设置，也可以像我一样，直接使用别人已经做好的配置。Notepad++的
自定义语言能够以XML格式导入导出。网上已经有人写好的适用于Notepad++的Markdown语法高亮规则，例如：  
[https://github.com/thomsmits/markdown_npp](https://github.com/thomsmits/markdown_npp)。  
从上述网址下载一个名为userDefineLang.xml的文件，在上述自定义窗口中导入该文件，即可实现对Markdown语法高亮的支持。

也可以手动将上述文件放置在%APPDATA%\Notepad++目录下（如果已有同名文件存在，则将下载文件的内容增加进去），并重启Notepad++。
