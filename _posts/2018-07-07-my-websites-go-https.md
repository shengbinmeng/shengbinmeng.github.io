---
title: 我的网站开始支持HTTPS
date: 2018-07-07 12:00:00
category: 技术
tags: [Web, 网站]
---

从今天起，这个博客网站（[blog.shengbin.me](https://blog.shengbin.me/)）和我的个人网站（[www.shengbin.me](https://www.shengbin.me/)）都开始支持安全连接，即通过HTTPS访问。

<!--more-->

这两个网站都是托管在[GitHub Pages](https://pages.github.com/)上的。在GitHub Pages上的站点，如果直接采用*.github.io域名，很早就可以开启HTTPS了。
但对于定制域名之前还不支持HTTPS，所以一直以来我的这些网站都只能通过不安全的HTTP协议访问。

直到最近，我发现GitHub已经[宣布](https://blog.github.com/2018-05-01-github-pages-custom-domains-https/)定制域名也支持HTTPS了。
他们与[Let's Encrypt](https://letsencrypt.org/)合作，使得更多网站增强了安全性。在此应该对这两家组织提供的各种便利表示感谢。

开启HTTPS的支持非常简单，没有额外的工作，只需在仓库设置里勾选上“Enforce HTTPS”即可。如下图所示：

![](/images/2018-07-07-github-pages-enable-https.png)

支持HTTPS是一个趋势。目前大多数浏览器都会在地址栏区分网站的安全性。Google的搜索引擎还会对开启了HTTPS的网站给予更多权重。
如今我也总算跟上了潮流。有些页面还因为引用了HTTP的外部资源被标记为不安全，等遇到了再逐步解决吧。以后就会对“http://”变得敏感，尽量避免使用了！