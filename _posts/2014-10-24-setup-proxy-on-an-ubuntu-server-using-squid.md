---
layout: post
title: 用Squid在Ubuntu服务器上设置代理
date: 2014-10-24 19:00:00
category: 技术
tags: [Linux, 代理]
---

得益于[GitHub的教育优惠](https://education.github.com/pack)，我搞了个位于国外的虚拟主机。本着“翻墙”的初衷，我在上面用[Squid](http://www.squid-cache.org/)设置了代理。

<!--more-->

## 安装Squid

机器是Ubuntu 14.04的系统，只需运行以下命令即可安装Squid（当前版本是squid3）：

    $sudo apt-get install squid
    
安装成功后Squid会自动开始运行，默认监听端口是3128。

## 简易配置

Squid的配置文件是/etc/squid3/squid.conf，编辑它可以对代理进行配置。

在下面这一行修改监听端口：

    http_port 3128

通过acl（访问控制列表）和http\_access来进行访问控制。由于我希望随时随地用来做代理，只需要简单地加一行`http_access allow all`即可。增加的控制指令需要位于`http_access deny all`之前。

重启服务以使新的配置生效：

    $service squid restart

## 用户验证

这样设置的代理任何人只要知道服务器地址和端口就能使用。为了限制滥用，我增加了用户验证。

在配置文件里增加如下配置：

    auth_param basic program /usr/lib/squid3/basic_ncsa_auth /etc/squid3/passwords
    acl authenticated proxy_auth REQUIRED

然后将之前配置的`http_access allow all`改为`http_access allow authenticated`。

这相当于告诉Squid根据文件/etc/squid3/passwords所存储的密码信息来进行用户验证。当然，这个文件还不存在，我们需要创建它并添加密码信息。这可以用一个叫做htpasswd的工具实现：

    $sudo htpasswd -c /etc/squid3/passwords <username>

选择一个用户名（\<username\>），该工具会提示你输入密码，然后将验证信息存入/etc/squid3/passwords文件。如果系统上没有htpasswd，可以通过以下命令安装：

    $sudo apt-get install apache2-utils
    
用户验证生效后，当通过这个代理访问网页时，浏览器将会询问用户名和密码。

## 翻墙？

后来发现，这样设置的代理无法翻墙！

我国伟大的网络防火墙（GFW）会通过域名来检测并拦截它不喜欢的访问请求（例如YouTube）。于是，虽然位于国外的代理服务器能取到YouTube的内容，但我的主机到代理服务器的通信会因为含有“youtube”而被拦截下来。因此，要想翻墙，必须要求从我的主机到代理服务器的所有通信都加密。一般来说，这就需要我主机上安装专门的客户端软件了（参考[ShadowSocks](https://github.com/clowwindy/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)的做法），只靠浏览器设置代理是不够的。

当然啦，代理不一定是用来翻墙的嘛，就如同翻墙不一定要用代理。
