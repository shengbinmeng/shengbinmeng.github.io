---
layout: post
title:  使用Jekyll和GitHub Pages搭建静态博客网站
date:   2013-07-16 00:00:01
categories: 技术
tags: [Web, Jekyll, Markdown, GitHub]
---

这个网站（[Shengbin's Blog](http://blog.shengbin.me)）是采用Jekyll和GitHub Pages搭建的。GitHub Pages可以免费提供静态页面的托管服务，其后台由Jekyll支持。关于如何建立站点，网上能找到很多说明。
这里主要归纳一下Jekyll是如何工作的。

<!--more-->

##涉及的几项技术

###Jekyll

[http://jekyllrb.com](http://jekyllrb.com)  
这是一个可以用来写博客的静态网站生成器。它接收一个特定结构的目录，读取、分析、处理其中的文件（包括md、html、txt、css、资源文件等），将其转换成一个静态站点。

###Liquid
  
[http://wiki.shopify.com/Liquid](http://wiki.shopify.com/Liquid)  
这是一个模版语言。其中有两类标记：Output和Tag。Output用于把变量值输出，Tag用于逻辑控制（其中有些是Block Tag）。{% raw %} Output例如"{{ site.author }}"，
Tag例如"{% if site.author == 'shengbin' %}...{% endif %}"，"{% assign variable = value %}"（前者是Block Tag）。{% endraw %}

###Pygment
  
[http://pygments.org](http://pygments.org)  
这是一个用Python实现的语法高亮的库。接收一段代码（Code），并指定其语言（Lexer）和输出文本格式（Formatter），生成代码高亮的带样式（Style）的输出文本（Text），如HTML。

###Markdown
 
[http://daringfireball.net/projects/markdown/](http://daringfireball.net/projects/markdown/)  
这是一种纯文本语言，用简单的标记来表示格式。它目的是为了方便地生成HTML，同时又比HTML更具备可读性和易写性。

###YAML

[http://yaml.org/](http://yaml.org/)  
这是一个数据序列化的格式，类似JSON。它主要用来表示结构化的数值，如列表，键值对等。在Jekyll中，它的作用在于提供front matter（详情如下）。

##网站生成过程

Front matter是放在一个文件最开头的几行文本。Jekyll对含有front matter的文本文件进行转换，对其他目录和文件则直接拷贝。
对于要转换的文本文件，例如每篇博客的md文件，进行如下处理：结合其中的front matter，将其嵌进模版布局文件中（Layouts），然后送给Liquid和Pygment。布局文件一般含有大量的Liquid标记，
提供基本的header和footer等结构，在其中填充内容后就成了一个页面。{% raw %} 该页面首先由Liquid进行处理，将其中的"{% %}"和"{{ }}"去除，得到HTML文本。这个过程中，有些Jekyll自定义的Tag，如"{% highlight ruby %}"，这些Tag需要Jekyll调用Liquid之外的工具。例如，对于上述highlight的Tag，Jekyll交给Pygment进行处理，得到HTML文本。
总之，Jekyll会将含有front matter的文件中需要处理的部分转换为HTML，从而得到想要的格式或内容。另一个需要指出的功能在于，Jekyll扫描了整个目录结构，因此它获得了一些全局的信息，使之可以在一个文件中使用。
例如，所有页面的列表、同一个分类下的全部文章，等等。正是这种全局扫描使撰写博客、生成站点变得自动化了，非常方便。

Jekyll对其要扫描的目录结构有一定的要求。你可以根据Jekyll的文档说明自己建立这些目录，也可以使用`jekyll new`命令生成。此外，已经有人为使用Jekyll建立静态博客网站提供了基础，这就是[Jekyll Bootstrap](http://jekyllbootstrap.com)。你目前看到的这个站点就是以Jekyll Bootstrap为起点建成的。
