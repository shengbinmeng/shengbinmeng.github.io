---
layout: post
title: LaTeX的参考文献系统
date: 2016-03-30 14:00:00
category: 技术
tags: [LaTeX]
---

用LaTeX写论文时，参考文献是一个重要部分。参考文献的排版涉及两个部分，一个是文献列表，一个是对文献的引用。
本文简单介绍一下LaTeX的参考文献系统。

<!--more-->

## 手动嵌入参考文献

如果参考文献较少，可以直接在LaTeX源文件内需要列参考文献的地方（一般是文档结尾处）手动添加如下的环境：

	\begin{thebibliography}
	\bibitem{lamport94}
	  Leslie Lamport,
	  \emph{\LaTeX: a document preparation system},
	  Addison Wesley, Massachusetts,
	  2nd edition,
	  1994.
	\bibitem{shengbin2016}
	  Shengbin Meng,
	  \emph{\LaTeX的参考文献系统},
	  http://blog.shengbin.me/latex-bibliography-system ,
	  2016.
	\end{thebibliography}
	
这与`\begin{equation}`、`\begin{figure}`等环境的用法类似，在其出现的地方插入文献列表。每个`\bibitem`表示一条文献的开始，紧接着出现的`{lamport94}`是这条文献的ID，
在需要引用它的地方用`\cite{lamport94}`命令即可。文献列表处的排版与别的文字一样，可以用`\emph`等命令当场指定其格式。引用处的排版为方括号内加数字，数字对应文献列表中的顺序编号。
除了默认的排版格式，还可以用natbib等宏包来增加更多的样式支持。

这种方法简单，但不够灵活。对于有大量参考文献的文档应该用下面介绍的专门工具。

## BibTeX

BibTeX通常指代一种文件格式和一个工具。这个文件以`.bib`为扩展名，用于存储参考文献数据。参考文献被分为不同的类别，例如文章（article）、书籍（book）、会议论文（inproceedings或conference）等。下面的例子表示一篇文章的数据。

	@article{greenwade93,
		author  = "George D. Greenwade",
		title   = "The {C}omprehensive {T}ex {A}rchive {N}etwork ({CTAN})",
		year    = "1993",
		journal = "TUGBoat",
		volume  = "14",
		number  = "3",
		pages   = "342--351"
	}
	
在类别（即article）之后的大括号中，第一项是该参考文献的ID，通过它来引用该条参考文献。后面各项是这一参考文献的信息。.bib文件中可以包含很多条类似上面这样的参考文献数据条目。这些数据组成了参考文献数据库。可以通过BibTeX提供的工具来使用该数据库。

这个工具在大多数LaTeX发行版里就是一个名为`bibtex`的可执行程序。它与`latex`程序合作进行参考文献的排版。在编译过程中，这些程序会分析LaTeX源文件，确定其中引用了哪些参考文献，然后从数据库中找到这些参考文献的信息，据此生成所需的`\begin{thebibliography}`环境。

使用BibTeX时，用户可以提供一个.bst文件来指定排版的样式。程序会根据这一样式文件来对`\begin{thebibliography}`环境中的内容以及引用的地方进行排版。

## BibLaTeX

BibLaTeX是最新的LaTeX参考文献处理系统，其目的是成为BibTeX的替代品。严格来说BibLaTeX只是一个宏包，但从取代BibTeX的角度看，我们可以认为它是个系统。

BibLaTeX沿用了BibTeX的参考文献数据库格式，并在其基础上进行了扩展。例如，增加了更多的文献类型，允许提供更丰富的参考文献信息，等等。

BibLaTeX的配套工具是一个功能更强大的可执行程序`biber`。作为一个宏包，BibLaTeX虽然也能与`bibtex`程序配合使用，但`biber`才是和它共同成长起来的最佳搭档。

不同于BibTeX使用一个.bst文件来指定所有样式，BibLaTeX将文献列表样式和引用样式分开处理，前者用.bbx文件来指定，后者用.cbx文件来指定。这样更模块化一些。
更重要的区别在于，.bst文件需要用另一种特定语言编写，而.bbx文件和.cbx文件只需用TeX语言编写，这使得BibLaTeX的样式定制要容易许多。

BibLaTeX逐渐会取代BibTeX。BibTeX在[它的介绍页面](https://www.ctan.org/pkg/bibtex)也鼓励用户尽可能切换到BibLaTeX。然而，由于BibTeX已经深深渗透到学术论文的写作和出版过程中，所以它的退位让贤估计还需要一段时间。

BibLaTeX和BibTeX的比较可参见网上的[一些讨论](http://tex.stackexchange.com/questions/25701/bibtex-vs-biber-and-biblatex-vs-natbib)。
