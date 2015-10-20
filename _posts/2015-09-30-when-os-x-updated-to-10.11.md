---
layout: post
title: 升级到OS X 10.11之后
date: 2015-09-30 12:00:00
category: 技术
tags: [OS X, Jekyll, Apple, Unix]
---

今天Apple正式向Mac用户推送了OS X 10.11（El Capitan）。升级到OS X 10.11之后，我的系统相应也作了一些变化。

<!--more-->

最先遇到的问题是一些工具（Jekyll、CocoaPods）不能用了。于是我试着重新安装Jekyll。它是用Ruby写的程序，通过RubyGems来安装。但我执行`sudo gem install jekyll`却失败了。错误信息如下：

```
ERROR:  While executing gem ... (Errno::EPERM)
    Operation not permitted - /usr/bin/bayes.rb
```

可以看到是权限问题。但我明明已经用root权限执行了，为什么仍然会受限呢？

调查发现，这是因为OS X 10.11加入了一个称为[System Integrity Protection](https://en.wikipedia.org/wiki/System_Integrity_Protection)（SIP）的新特性。
升级之后，目录`/usr/bin/`成为了被保护的目录之一，只能由操作系统来管理。SIP又被称为rootless，也就是说，对于被保护的目录，即使root用户也无法改写。这就彻底堵死了第三方代码修改重要目录的机会。
这也是为什么我重新安装jekyll未能成功的原因。之前已经安装在`/usr/bin/`的第三方可执行文件（例如`/usr/bin/jekyll`）在系统升级时都被删了，这也解释了为什么一些工具变得不能使用了。

那该如何是好呢？

好在，Apple考虑到了Unix的传统，将`/usr/local/`开放给了第三方程序（整个`/usr/`下，只有这一个没被SIP保护的目录）。开发者只能将这个目录作为程序安装目录了。
对于那些用Ruby、Python等脚本语言写的程序，Apple官方[文档](https://developer.apple.com/library/mac/documentation/Security/Conceptual/System_Integrity_Protection_Guide/FileSystemProtections/FileSystemProtections.html#//apple_ref/doc/uid/TP40016462-CH2-SW1)
里还专门有段话鼓励开发者自己安装这些语言和及其组件，而不要使用系统自带的版本。看来，系统不愿与我们共享，我们只能靠自己了！

对于Jekyll的安装问题，GitHub上的一些[讨论](https://github.com/jekyll/jekyll/issues/3984)[页面](https://github.com/jekyll/jekyll/pull/3999)给出了建议的[解决方法](http://jekyllrb.com/docs/troubleshooting/#jekyll-amp-mac-os-x-1011)。
实质上就是将原来安装在`/usr/bin/`下面的可执行文件jekyll转而安装在`/usr/local/bin/`下。要如此，可以执行下面的命令：

```sh
sudo gem install -n /usr/local/bin jekyll
```

或者按照Apple官方文档的建议，不使用系统自带的Ruby和RubyGems，而是用Homebrew来安装和维护自己的一份：

```sh
brew install ruby && gem install jekyll
```

我采用了后一种方法。

趁此机会，我干脆将我在OS X上的程序包管理工具由MacPorts换成了Homebrew。一年以前，我写了一篇[文章](/posts/macports-or-homebrew/)比较MacPorts和Homebrew。
现在看来，后者已经成为新宠，而且表现不俗。长江后浪推前浪，后起之秀取代其前辈也在情理之中吧。

我不愿喜新厌旧，但也只能顺势而为。