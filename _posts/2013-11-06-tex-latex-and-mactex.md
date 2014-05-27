---
layout: post
title: TeX，LaTeX，以及MacTeX
date: 2013-11-06 22:00:0
category: 学术
tags: [LaTeX, Mac, OS X]
---
{% include JB/setup %}

准备在Mac上写学术论文了，先得为OS X安装一下LaTeX。
LaTeX项目官网[告诉](http://latex-project.org/ftp.html)我说LaTeX可以随TeX分发包一起获取，而OS X上的TeX分发包可以选用MacTeX。

工欲善其事，必先利其器。欲利其器者，必先知其器。TeX、LaTeX、MacTeX，它们具体是什么？它们之间又是什么关系呢？

<!--more-->

##TeX

首先，TeX是最早由[Donald Knuth](http://baike.baidu.com/view/2193127.htm)开发的Unix计算机程序。它是一个排版引擎，能够用来生成漂亮的文档。

其次，TeX还是一门编程语言。当然，这语言也是为文档写作和排版服务的。TeX语言提供300来条原始排版命令，称为“控制序列”（control sequence），
可以用来控制格式。例如：控制序列`\sl`表示斜体（英文slanted），`\bf`表示粗体（英文bold）。
程序员觉得直接用这些琐碎的控制序列略麻烦，于是就把多个控制序列组合起来产生了“宏”（macro）。例如，宏`\heading`就是`\sl`和`\bf`
的组合，表示粗斜体（顾名思义，`\heading`宏适用于标题）。

最后还应指出，TeX是下面要介绍的东西的基础。没有TeX，就没有其他。

##LaTeX

上面说到，一般大家嫌直接使用TeX控制序列麻烦，都会选择使用宏。于是就有人编写了一些好用的宏或者适用于特定文体的宏，将它们组合起来发布。
这样集在一起的宏的组合被称为“格式”（Format）。LaTeX就是一种格式。

LaTeX是专为技术文档设计的格式。使用它可以很方便地输入数学公式、插入图和表格，以及添加参考文献。
LaTeX还支持导入额外的“包”（package），从而无限扩展其功能以实现特殊的目的。由于以上特点，LaTeX成为写学术论文之必选。
目前LaTeX版本是2e，所以经常见到LaTeX2e的称呼；据说LaTeX 3正在开发中。

除了LaTeX外，还存在其他的格式。例如，ConTeXt是一种用于通常出版的格式，特点是非常结构化，使作者可以专注于内容；
TeX的开发者最早也开发了一种格式，叫做Plain TeX；后人在Plain TeX基础上进行扩展，就出现了Eplain TeX；等等。

##MacTeX

我在Mac上安装的是MacTeX。这是一个TeX的分发包。MacTeX的基础是TeX Live，一个多平台的TeX发行版。
这个分发包巨大，多达几个G！这怎么也不像是Unix程序分发包的风格呀。确实，MacTeX不仅包含原来的Unix程序TeX，还包含许多别的内容。

MacTeX安装的内容有：

* TeX Live 2013，主要的TeX程序，还附带各种Format、Package、Style文件等。LaTeX就在其中。
* 两个TeX前端（类似IDE的东西），即TeXworks和TeXShop。
* 一些GUI辅助应用，如检测更新的TeX Live Utility、拼写检查的Excalibur、管理参考文献的BibDesk、为别的应用生成公式的LaTeXiT等。
* 一些命令行工具，如Ghostscript-9.07（用于由ps生成pdf）、ImageMagick-Convert-6.8.3（用于不同格式间的转换）等。
* 一些字体和文档（manual）。

关于TeXworks和TeXShop：一个分发包里竟有两个同样功能的前端程序，这已经让我不满了；打开TeXworks之后发现其界面在Retia显示屏上简直惨不忍睹，于是果断删除之！
原来TeXShop才是给Mac用的前端，TeXworks是基于TeXShop进行多平台支持的尝试（话说你还没做好就别跟着TeXShop一起发布了行不行！）。

最后说一句：在Windows上我用的Tex前端是[TeXstudio](http://texstudio.sourceforge.net/)，但由于它的作者们都不用Mac，所以其Mac版体验并不好。
