---
layout: post
title: 在OS X上编译ffmpeg和ffplay
date: 2013-10-30 20:00:0
category: 技术
tags: [Mac, OS X, x264, FFmpeg]
---
{% include JB/setup %}

[FFmpeg](http://ffmpeg.org/)是一个著名的开源音视频工具包。ffmpeg是其中的音视频处理程序，ffplay是其中的简易媒体播放器。
为了在OS X上编译出可用的ffmpeg和ffplay，还是可能会遇到一些麻烦事的。
下面是我今天在最新的OS X 10.9 Mavericks上编译FFmpeg的过程，包括遇到的问题和解决方法。

<!--more-->
我需要FFmpeg支持H.264/AVC视频编码，因此要链接外部库[x264](http://www.videolan.org/developers/x264.html)。
首先编译x264并将其安装在系统目录中，这一步没什么问题。然后如下配置FFmpeg（--enable-gpl是因为x264要求GPL协议）:

    ./configure --enable-libx264 --enable-gpl

配置结果失败了！提示找不到libx264的错误。明明已经将x264的头文件和动态库都安装在了`/usr/local/`下面，为什么找不到呢？注意到configure的输出里面有以下信息：

    Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include/c++/4.2.1
    
看上去似乎是`/usr/local/`不包含在索路径中的样子。那为保险起见，干脆显式指定好了：

    ./configure --enable-libx264 --enable-gpl --extra-cflags="-I/usr/local/include" --extra-ldflags="-L/usr/local/lib"

这样还真就配置通过了！而且看到libx264已经启用了。接着编译FFmpeg，结果ffmpeg生成了，但ffplay却没有生成。

网上搜索了下，原来ffplay要显示视频，需要[SDL](http://www.libsdl.org/)。我看了一眼FFmpeg的configure输出，果然SDL没有启用。
于是安装了最新的SDL后重试，结果显示还是没有启用！这时在config.log中搜索"sdl"发现了如下信息：

    pkg-config --exists --print-errors sdl
    Package sdl was not found in the pkg-config search path.
    Perhaps you should add the directory containing `sdl.pc'
    No package 'sdl' found
    
看来没找到sdl这个包。其中提到了“pkg-config search path”和“sdl.pc”，于是我去`/usr/local/lib/pkgconfig/`看了看，发现里面有个sdl2.pc。
我强行把sdl2.pc重命名为sdl.pc试了试，config.log中的信息变了：

    #error "unsatisfied condition: (SDL_MAJOR_VERSION<<16 | SDL_MINOR_VERSION<<8 | SDL_PATCHLEVEL) >= 0x010201"
    
看来是虽然找到了sdl但抱怨说版本不对。嗯，因为它找到的sdl.pc是我从sdl2.pc改过去的，看来我装了SDL 2.0，而FFmpeg只能用更低版本的SDL。

好吧，去SDL官网下载了历史稳定版本v1.2。编译时出现了以下错误：

    fatal error: 'X11/Xlib.h' file not found
    
突然想起来Apple[宣布从OS X 10.8开始系统已经不自带X11](http://support.apple.com/kb/ht5293)了，还需自己从[官网](http://xquartz.macosforge.org/)安装。
装好了X11，编译SDL1.2时可以找到头文件了，但是出现了进一步的错误！还真是坎坷呀：

    ./src/video/quartz/SDL_QuartzVideo.h:94:5: error: unknown type name 'CGDirectPaletteRef'

这个SDL的编译错误竟然是由最新的OS X 10.9导致的，参见SDL开发版本库里的[一次提交](http://hg.libsdl.org/SDL/changeset/bbfb41c13a87)。
这是一个月前的一个修复，看来还没有打包到稳定版本中，我就自己按照这次提交来修改代码吧。改之后make通过了，但在make install时又出现了一个错误：

    ./src/video/x11/SDL_x11sym.h:168:17: error: conflicting types for '_XData32'

行吧，同样的原因和方法，又按照[这次提交](http://hg.libsdl.org/SDL/rev/91ad7b43317a)修改了代码，总算完成了SDL1.2的编译和安装。

功夫不负有心人，安装SDL1.2之后再次配置FFmpeg时便显示sdl已经启用。接下来就顺利地编译并安装了我想要的FFmpeg。

成功后的效果是这样的：

    Shengbin-Mac:~ shengbin$ ffplay
    ffplay version N-57608-g4427fe7 Copyright (c) 2003-2013 the FFmpeg developers
      built on Oct 30 2013 23:19:05 with Apple LLVM version 5.0 (clang-500.2.79) (based on LLVM 3.3svn)
      configuration: --enable-libx264 --enable-gpl --extra-cflags=-I/usr/local/include --extra-ldflags=-L/usr/local/lib --enable-shared
      libavutil      52. 48.100 / 52. 48.100
      libavcodec     55. 39.100 / 55. 39.100
      libavformat    55. 19.104 / 55. 19.104
      libavdevice    55.  5.100 / 55.  5.100
      libavfilter     3. 90.100 /  3. 90.100
      libswscale      2.  5.101 /  2.  5.101
      libswresample   0. 17.104 /  0. 17.104
      libpostproc    52.  3.100 / 52.  3.100
    Simple media player
    usage: ffplay [options] input_file

最后想说一下，本文的价值在于解决问题的过程，所遇到的这些问题本来应该由相应项目的开发维护人员来处理的。还是希望不要再有这些麻烦事。