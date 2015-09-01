---
layout: post
title: 在OS X 10.10上无法启动Matlab的原因和解决方法
date: 2014-11-25 12:00:00
category: 技术
tags: [OS X, Matlab]
---

许久不用Matlab，今天在Mac上想用的时候却发现无法启动了。最近刚把系统升级到了OS X 10.10，应该是这次升级导致了原来能用的程序出了问题。

<!--more-->

我机器上装的是Matlab_R2011b，尝试启动时的错误提示如下：

“MATLAB cannot be opened because of a problem. Check with the developer to make sure MATLAB works with this version of OS X.”

这样的话虽然是套话，不过很容易让人相信了是该程序与新版本系统不兼容（明显是升级系统导致的！）。

我去网上搜索了一下，发现官方针对新的OS X系统提供了补丁：<http://www.mathworks.com/support/bugreports/1098655>。我装了这个补丁后，Matlab仍然无法启动，而且错误提示没变。
这个补丁页面上所说的问题是Java抛出了异常，看上去并不像是程序无法启动的原因（我也没遇到这个异常）。不过既然是补丁，还是装上为好。

接下来只能进一步寻找罪魁祸首了。最好用的办法就是运行程序的命令行启动，然后看Log：

	Shengbin-MacBook:~ shengbin$ /Applications/MATLAB_R2011b.app/bin/matlab
	dyld: Library not loaded: /usr/X11/lib/libXext.6.dylib
	  Referenced from: /Applications/MATLAB_R2011b.app/bin/maci64/libmwhg.dylib
	  Reason: image not found
	Trace/BPT trap: 5
	
这下就发现问题了：matlab程序试图加载X11的一个库却没找到。它以为X11在/usr目录下，但其实新版本的OS X系统已经不自带X11了，需要自己安装
（参见我的另一篇博客：[《在OS X上编译ffmpeg和ffplay》](http://blog.shengbin.me/posts/build-ffmpeg-on-os-x/)），
而新安装的X11被放在了/opt/X11，无怪乎它找不到了。最简单的解决方法就是创建一个连接“/usr/X11”指向“/opt/X11”：

	sudo ln -s /opt/X11/ /usr/X11
	
如此，Matlab即可正常启动。

Bonus：

OS X上的应用程序XXX.app是个文件包，里面有个bin目录，bin目录下有这个程序的可执行文件。用Terminal来执行它们，大有益处。

我惊奇地发现，Matlab程序bin目录里的matlab竟然是个脚本（而不是二进制文件）！有图为证：

![](/images/2014-11-25-matlab-script.png)

不过转念一想，这有啥好惊奇的嘛……