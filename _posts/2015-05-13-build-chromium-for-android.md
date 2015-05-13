---
layout: post
title: 编译Android版Chromium
date: 2015-05-13 12:00:00
category: 技术
tags: [Android, Chrome, Google]
---
{% include JB/setup %}

[Chromium](https://www.chromium.org/)项目现在还不提供编译出完整的Android版Chrome的途径。针对Android平台的编译仅限于一些简化版的外壳程序，例如Content Shell、Chrome Shell、WebView Shell等。
我的目标是编译出ChromeShell.apk。

<!--more-->

[官网的说明页面](https://code.google.com/p/chromium/wiki/AndroidBuildInstructions)里介绍了详细的步骤，这里就不重复了。要指出的主要有两点。

第一点比较简单，就是要确保网络能翻墙。Google的资源很多都在墙外，而编译过程中的步骤需要访问这些资源，所以翻墙是必备的。

第二点是要避免我所遇到的坑。我之前曾经[编译过桌面版的Chrome](/posts/compile-chrome/)，因此已经有了大部分代码。然后我看到官网说明页面上有下面这段话：

	NOTE: if didn't do fetch android to get your code (e.g. you're reusing a desktop checkout), you'll likely need to do:
	~/chromium$ echo "target_os = ['android']" >> .gclient && gclient sync

也就是说可以不必用`fetch android`来重新下载代码，而是可以重用已有的桌面版代码。于是我就这么做了，但编出来的APK却与[官方编出来的]()怎么也不一致！

无奈之下我去相关的Google讨论组[询问](https://groups.google.com/a/chromium.org/forum/#!topic/chromium-discuss/0IAwnW-ysXs)，结果发现原来是因为我的代码没有更新到最新版。
我是反复确认执行了`gclient sync`，但后来才知道这条命令并不会更新代码。要将代码更新到最新版，要么重新执行`fetch`，要么使用`git pull`。这就是比较坑的地方：同样是Google提供的工具，
`repo sync`明明就是用来更新代码的！当然，主观方面则要归咎于先入为主的成见。

如果之前从没编译过Chromium，按照官网的说明一步步来，反倒不会出现什么问题。
