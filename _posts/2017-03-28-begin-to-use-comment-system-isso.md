---
title: 开始采用评论系统Isso
date: 2017-03-28 12:00:00
category: 技术
tags: [Web, Python]
---

这个博客之前采用的评论系统[多说](/posts/the-duoshuo-comment-system-of-this-blog)宣布要倒闭了。
于是我这两天寻找了一个新的替代方案：[Isso](https://posativ.org/isso/)。

<!--more-->

在使用多说的过程中遇到过不少问题，但一直凑合能用，而且这个博客的评论并不多，所以一直也没太管。现在多说要停止服务了，只能另选一个。

多说的倒闭让我更加觉得第三方服务真的不是长久之计！博客系统不是，评论系统也不是。惟一长久的是自己搭建服务，程序和数据都自己掌握。

于是这次我就按这个原则来找，最终选用了Isso。Isso是一个Python写的Web应用，而且是开源的，可以将它部署在自己的服务器上，实现与多说或[Disqus](https://disqus.com/)一样的功能。

我在自己的DigitalOcean云主机上部署了一份。参照[官方文档](https://posativ.org/isso/docs/)和网上的一些[资料](http://blog.pythonity.com/how-to-use-isso.html)，并没有什么大的问题。
惟一的问题是遇到了“Invalid command 'ProxyPass'”的错误，也通过简单地搜索找到了[解决方法](http://askubuntu.com/questions/58179/install-mod-proxy-to-get-proxypass-to-work)。
在部署过程中，我还改善了Isso对中文语言的配置选项，并向官方提交了[拉取请求](https://github.com/posativ/isso/pull/316)。

现在新的评论系统已经可用了。相比于以前更加简洁（相应地没那么美观了），而且没有了登录，评论者自行指定自己的身份，不指定的话默认匿名留言。
可以对一个评论进行好评和差评，也算是增加了一种互动方式。还不错！

之前存在多说的评论数据我导出了，但还没来得及设法导入到Isso中。其实多说导出的格式能否导入Isso还不可知。如果最终没能导入成功，那之前的评论就只有我自己留着，不再显示了。

虽然现在写博客的人少了，读博客的人少了，评论博客的人更少了，但还是那句话：欢迎留言，或任何形式的交流。
