---
layout: post
title: 为GitHub Pages自定义域名
date: 2013-08-20 20:00:0
category: 技术
tags: [Web, GitHub, 域名]
---
{% include JB/setup %}

最近彻底搞清楚了GitHub Pages自定义域名的机制和使用方法。

<!--more-->
这个博客站点托管在GitHub Pages上，本来的域名是shengbinmeng.github.com，假设我想要将它定制为blog.shengbin.me（事实上我已经这么做了）。

首先，我需要到我的域名设置页面（或者说在shengbin.me这个域的Zone File中）添加一条CNAME记录，使blog.shengbin.me指向shengbinmeng.github.com。这样的话，当浏览器向DNS
系统询问blog.shengbin.me的IP地址时，DNS系统就会转而去找shengbinmeng.github.com的IP地址。GitHub公司在github.com这个域内也添加了一条CNAME记录，
使shengbinmeng.github.com（包括所有其他用户的类似域名）指向github.map.fastly.net（Fastly是做CDN和网站加速的公司，GitHub Pages使用了他们的服务）。
运营fastly.net的公司负责将github.map.fastly.net解析为103.245.222.133，也就是GitHub Pages（其中一个？）服务器的IP地址。

上述信息可以从dig命令的输出结果得到。

    shengbin@Shengbin-VirtualBox:~/websites/shengbinmeng.github.com$ dig blog.shengbin.me

    ; <<>> DiG 9.8.1-P1 <<>> blog.shengbin.me
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15603
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 4, ADDITIONAL: 4

    ;; QUESTION SECTION:
    ;blog.shengbin.me.		IN	A

    ;; ANSWER SECTION:
    blog.shengbin.me.	809	IN	CNAME	shengbinmeng.github.com.
    shengbinmeng.github.com. 809	IN	CNAME	github.map.fastly.net.
    github.map.fastly.net.	30	IN	A	103.245.222.133

    ;; AUTHORITY SECTION:
    
    ...
    
浏览器根据DNS系统告诉它的GitHub Pages服务器地址来请求页面内容。GitHub Pages服务器不仅提供标准的Web服务，还会分析用户页面代码中的CNAME文件来支持定制域名。
我按照要求在我的页面代码仓库中增加名为“CNAME”的[文件](https://github.com/shengbinmeng/shengbinmeng.github.com/blob/master/CNAME)，其内容为：

    blog.shengbin.me
    
GitHub Pages服务器从这个文件得知shengbinmeng.github.com被定制为了blog.shengbin.me，于是它让浏览器在地址栏显示blog.shengbin.me。在用户看来，相当于直接访问了blog.shengbin.me。

在GitHub Pages的[帮助文档](https://help.github.com/articles/setting-up-a-custom-domain-with-pages)中也给出了使用一级域名的方法。由于一级域名不能被CNAME，只能通过A记录直接指向服务器IP地址。
文档里还提到，如果一级域名和www子域名都指向GitHub Pages服务器，那么这二者之间是互相等价的（[www.shengbin.me](http://www.shengbin.me)与[shengbin.me](http://shengbin.me)就是如此）。

最后提醒一点，GitHub Pages的CNAME文件中指定的域名必须在所有用户的所有页面中保持唯一性，否则页面生成会失败。这也是很合理的，你的域名怎么可能会和别人一样呢，对吧？
