---
layout: post
title: 在Ubuntu 12.04上安装Grunt
date: 2014-05-28 19:00:00
category: 技术
tags: [Linux]
---


JavaScript任务运行工具[Grunt](http://www.gruntjs.org/)类似GNU Make，可以自动化地执行一些任务。今天在我的Ubuntu 12.04系统上安装Grunt时遇到了一些问题。
下面是这些问题及其解决方案。

<!--more-->

我们要执行的安装命令是：

    npm install -g grunt-cli

遇到的问题有两个。

第一个问题在官方网站的[安装说明](http://www.gruntjs.org/docs/getting-started.html)里已经提到了，即[Node.js](http://nodejs.org/)的版本过低。

我的系统里没有npm，在用`sudo apt-get install npm`安装npm时，会自动安装Node.js，可是这时候安装的Node.js版本为0.6。Grunt要求Node.js版本至少为0.8，所以这时安装会失败（而且错误信息比较诡异）。
看来Ubuntu的软件仓库里Node.js更新不及时。解决方法就是去<http://nodejs.org/>下载最新版本。我不小心下载了源代码，就自己`configure & make install`了，其实是可以下载二进制的。

第二个问题是网络原因导致的，提示“Error: tunneling socket could not be established, cause=socket hang up”。

根据StackOverflow上的[一个问答](http://stackoverflow.com/questions/12724974/packages-not-installing-error-tunneling-socket-could-not-be-established)，执行`npm config set registry http://registry.npmjs.org/`之后再安装就可以了。安装程序默认访问的是安全链接<https://registry.npmjs.org/>，我直接在浏览器里输入这个网址也无法访问，改为非安全连接<http://registry.npmjs.org/>就可以。

解决以上问题之后，`npm install -g grunt-cli`就成功执行了。
