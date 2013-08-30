---
layout: post
title: 关于域名解析
date: 2013-08-30 20:00:0
category: 技术
tags: [Web, 域名]
---
{% include JB/setup %}

之前在计算机网络课上曾详细学过域名系统（Domain Name System， DNS）。最近又重新了解了一下其相关知识和实际过程，并按过程中事件发生的顺序解释于此。

<!--more-->
每个操作系统内部集成有一个DNS解析器（DNS Resolver，DR），我们称之为DR0。别的应用程序问它一个域名的IP地址是什么，它必须回答xxx.xxx.xxx.xxx。

DR0首先检查它本机上的缓存，如果有答案，直接回复给应用程序。否则DR0通过向一个DNS服务器（DNS Server，DS）发起DNS查询 （DNS Query，DQ）来获取答案。我们把DR0直接联系的那个DS称为DS1。DS1的IP地址由用户在TCP/IP属性里手动设置（或者由DHCP自动设置）。

    DNS查询分为递归性的和非递归性的（又称迭代性的）。递归性的查询要求它的接收者DS直接回复最终的IP地址，也就是把查询任务整个推托了服务器端。对于非递归的查询，服务器可能只回复上一级域名对应的DNS服务器是谁，让DR自己再去问。
    DR与DS之间可以协商到底是用递归性的还是非递归性的查询。但是几乎所有操作系统中集成的DR都是简化版的解析器，它们比较笨，或者说比较耍大牌，只发递归性的DQ。

DR0发出的查询是递归性查询，也就是要求为它服务的DS1必须直接回复最终的IP地址。DS1也先检查它本机上的缓存，如果发现答案，就直接回复给DR0。否则，它也会启动一个DR去进行解析，我们称之为DR1。

DR1是由专门的软件实现，比集成在操作系统里的要复杂些。它发出的DQ一般是迭代性的，也就是按照下面的逻辑一步步询问，直至获取到目标IP地址。当然，在每一步中都会有缓存机制，如果之前查询过就不必每次重新查询了。

我们以[www.shengbin.me](http://www.shengbin.me)为例进行说明。

首先，DR1根据其预存的Root Name Servers的IP地址找到其中一个[根服务器](http://en.wikipedia.org/wiki/Root_Name_Server)，向它询问顶级域me的权威域名服务器DS2的IP地址。

    根服务器就是传说中全球一共13个的那些域名服务器。它们的IP地址是公开知道的，每个主机都会预先存一份。权威域名服务器上存储着某个域上名字解析的权威信息。
    所谓权威是说这些信息都是原创的（可以简单认为是由人设置的）。这些信息的存储采用一种称为Zone File的文件。配置这个域的域名信息就是编辑对应的Zone File。

然后DR1根据回复的IP地址去找DS2，向它询问shengbin.me这个域的权威域名服务器DS3的IP地址。

    DS2是me域的权威域名服务器，它提供的所有xx.me的信息都是人为配置的。例如，我向域名提供商Goddady注册shengbin.me这个域名时，Goddady向DS2配置了
    如下信息：shengbin.me的权威域名服务器DS3是ns75.domaincontrol.com（还有一个备用的，这里略去），并且IP地址是216.69.185.48。
	
接下来DR1根据回复的DS3的IP地址去找DS3，向它询问www.shengbin.me的IP地址。

    DS3是shengbin.me这个域的权威域名服务器，它提供的所有xx.shengbin.me的信息都是人为配置的。事实上，是由我配置的。我在Goddady的网站上可以更改这些配置信息。
	
如果我把www.shengbin.me配置为一条A记录，也就是直接指定它的IP地址，那么DS3就会回复DR1这个IP地址。DR1终于得到了它想要的IP地址，整个查询过程就结束了。
但我没有这样配置，我是把www.shengbin.me配置成了一条CNAME记录，也就是为它指定了一个别名shengbin.github.io。于是DS3回复DR1说，“你去找shengbin.github.io吧，它就是www.shengbin.me”。

接下来DR1要继续进行类似上面的过程去查询shengbin.github.io的IP地址，直至它得到结果。它把结果回复给DR0，然后把结果缓存起来，才算是完成了这一次漫长而复杂的任务（不过有了缓存，下次它就省事了）。

DR0接到DR1返回的结果后，向应用程序交差。

DNS是一个复杂而庞大的系统，以上这些只是大概和皮毛。为了让你通过[blog.shengbin.me](http://blog.shengbin.me)看到这个博客，DNS系统在背后做了不少工作呢！

本文知识主要来源为<http://en.wikipedia.org/wiki/Domain_Name_System>（DNS的维基百科）和<http://www.zytrax.com/books/dns/>（一本网络书籍）。
