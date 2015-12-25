---
layout: post
title: Git提交信息撰写风格
date: 2015-12-25 12:00:00
category: 技术
tags: [Git, 编程]
---

是时候固化一下我撰写Git提交信息（commit message）时的风格了。如果你对下面所列的几点没有异议，你也可以考虑遵循之。

<!--more-->

### 1. 使用英文

这与编程时写注释是一样的要求。编程语言是基于英文的，就该入乡随俗。

例如，写成：

	Fix a bug
	
而不要写成：

	修复了一个bug

你看，连bug对应的汉语都翻译不出来，还不直接用英文！

### 2. 提交信息分为标题和正文

标题只有一行且不应太长。对于简单的提交来说，只有一句标题即可。如果有很多要说的，就写在正文里（Git规定正文与标题之间以空行分隔）。

Git的提交信息被设计为可以直接转换成一封邮件发送，标题行是邮件的主题，正文是邮件内容。
撰写提交信息时，应该尽量与这个设计原则相符。

下面是一个广为流传的Git提交信息样本。它陈述且示范了撰写Git提交信息的最佳实践。

	Capitalized, short (50 chars or less) summary

	More detailed explanatory text, if necessary.  Wrap it to about 72
	characters or so.  In some contexts, the first line is treated as the
	subject of an email and the rest of the text as the body.  The blank
	line separating the summary from the body is critical (unless you omit
	the body entirely); tools like rebase can get confused if you run the
	two together.

	Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
	or "Fixes bug."  This convention matches up with commit messages generated
	by commands like git merge and git revert.

	Further paragraphs come after blank lines.

	- Bullet points are okay, too

	- Typically a hyphen or asterisk is used for the bullet, followed by a
	  single space, with blank lines in between, but conventions vary here

	- Use a hanging indent

### 3. 标题首字母要大写

英语中首字母一般都是大写的。编程时写注释也是同样的要求。

例如，写成：

	Fix a bug
	
而不要写成：

	fix a bug

### 4. 标题末尾不要用句号

例如，写成：

	Fix a bug
	
而不要写成：

	Fix a bug.

如果加了句号，就相当于邮件主题栏以句号结尾，这是不符合惯例的。另外，如果带句号，`git revert`操作默认产生的标题行会是这样的：

	Revert "Fix a bug.".

十分难看。提交信息的标题在很多地方都会用到，不带句号会更美观。

### 5. 标题尽量用祈使句

简单来说，就是以动词原形开头。

例如，写成：

	Fix a bug
	
而不要写成：

	Fixed a bug

这是为了与一些Git操作默认产生的提交信息（包括“Merge ...”和上面提到的“Revert ...”等）一致。

要求标题用祈使句有时候会显得勉为其难。可以通过填充下面的句子来帮助思考：“If applied, this commit will xxx”，其中xxx就是所需的祈使句。

事实上，Git默认产生的提交信息也有不遵循这一规范的特例：“Initial commit”。

### 6. 正文要物有所值

Git提交信息的正文与程序中的注释在这方面是类似的。正文不是必须的，而且增加额外的工作量，所以应判断一下是否需要写，要写就好好写。
正文内容最好侧重于说明这一提交有哪些改动，并解释为什么要做这些改动，实现细节还是应留给代码。

如同编程时的代码风格一样，Git提交信息的撰写风格也值得重视。在同一个项目中，开发者最好能做到风格统一。