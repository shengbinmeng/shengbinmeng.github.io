---
title: Mac OS X主机名称变化
date: 2016-09-08 12:00:00
category: 技术
tags: [OS X, Mac, DNS]
---

今天公司搬到了新的办公楼。在新办公室使用Mac时，我发现Terminal（命令行终端）里显示的主机名由原来的“Shengbins-MacBook-Pro”变成了“localhost”。
我想起来之前在家里也遇到过这个问题，于是便好好搜索了一下原因，并最终将其解决了。

<!--more-->

首先需要明确的是终端显示的主机名是环境变量$HOSTNAME的值（更准确地说只是其第一个句点前的部分）。
这个变量也是`hostname`命令在显示和修改时访问的对象。
之前我有一篇[文章](/posts/change-hostname-on-ubuntu)介绍过如何用`hostname`命令在Linux系统上修改主机名。
虽然OS X是BSD而非Linux一派的，但这个命令却是共有的。

那么这个变量的值是如何确定的呢？又怎么会变呢？

对于OS X来说，它会按[如下的顺序](http://superuser.com/a/346722)来确定主机名，直到获取到为止：

1. 从以下文件中读取：/etc/hostconfig
2. 从以下系统配置项读取：/Library/Preferences/SystemConfiguration/preferences.plist中的System ▸ System ▸ HostName
3. 由本机IP地址的反向DNS查询获取
4. 从以下系统配置项读取：/Library/Preferences/SystemConfiguration/preferences.plist中的System ▸ Network ▸ HostNames ▸ LocalHostName
5. 如果以上方法都没获取到，就简单地定为“localhost”

在我的系统上，第1、2两项都没有设置，第4项设为了“Shengbins-MacBook-Pro”。
根据以上顺序，主机名变为“localhost”应该就是第3项导致的。IP地址的反向DNS查询结果取决于所接入网络的DNS服务器，所以在不同环境下会不一样。
在旧办公室时，这个查询没有返回结果，所以就用了第4项的结果；在新办公室时，这个查询返回了“localhost”，所以就显示了这个。
第5项虽然也得到“localhost”的结果，但它排在4之后，所以不是它的原因。

在新办公室我电脑的IP地址是192.168.3.101，对其进行反向DNS查询（DNS查询是由名称得到IP地址，反向DNS查询与之相反，是由IP得到名称）的结果如下：

	Shengbins-MacBook-Pro:~ shengbin$ nslookup 192.168.3.101
	Server:		192.168.0.1
	Address:	192.168.0.1#53

	101.3.168.192.in-addr.arpa	name = localhost.

可以看出，得到的主机名正是localhost。我还试了所在局域网内的其他IP，进行反向DNS查询都会得到这个localhost主机名。
这种没有区分度且毫无意义的结果是由比较傻的DNS服务器（即上面结果输出中的192.168.0.1，当然更可能是人做的配置比较傻）提供的。
严格来说，这种结果是错误的。因为内网IP在in-addr.arpa数据库里确实是没有条目的。
下面我们指定用比较聪明的DNS服务器（Google的8.8.8.8）来查询，结果就是“未找到”，这才是正确的：

	Shengbins-MacBook-Pro:~ shengbin$ nslookup 192.168.3.101 8.8.8.8
	Server:		8.8.8.8
	Address:	8.8.8.8#53

	** server can't find 101.3.168.192.in-addr.arpa.: NXDOMAIN

在旧办公室应该就是返回了这样“未找到”的结果，所以确定主机名时就跳过了上面顺序中的第3项，采用了第4项，也正符合用户的预期。

现在问题搞清楚了，解决起来也就容易了。

第一种方法是在第3项之前，也就是通过第1、2两项，设置你想要的主机名。以第2项为例，可执行：

	sudo scutil --set HostName "Shengbins-MacBook-Pro"

但这不是一个理想的方法，因为使用命令行写配置文件对用户来说很不直观友好。相比而言，第4项的LocalHostName除了可以像上面那样用`scutil`来设置，
还可以在图形界面的系统设置（System Preferences -> Sharing）里修改，如下图：

![](/images/2016-09-04-system-preferences-sharing.png)
（_系统偏好设置->共享_）

点击编辑按钮就可以修改“xxx.local”，这里面的xxx就是LocalHostName。顺便说一句，图中的Computer Name是可读性更好的计算机名称（允许空格等其他字符），
在preferences.plist文件里也有配置项（System ▸ System ▸ ComputerName），同样可以通过`scutil`来存取，不过它跟本文涉及的主机名没太大关系。

因此，更好的方法是设法跳过第3项，从而让第4项生效。这需要使反向DNS查询能正确地对局域网内IP给出“未找到”这一结果。

最佳途径自然是更改所接入网络的DNS服务器配置让它更聪明，这样可以惠及局域网内所有主机。
如果这行不通（例如网络管理员觉得本文通篇都在胡扯），那只能独善其身，把自己电脑所用的DNS服务器换成更聪明的了（例如Google的8.8.8.8）。
