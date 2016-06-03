---
layout: post
title: 从Chromium中抽取Android ChromeShell
date: 2015-06-09 12:00:00
category: 技术
tags: [Android, Chrome, Google]
---

在之前的[一篇文章](/posts/build-chromium-for-android/)中，我介绍了Android版Chromium的编译。当时[Chromium](https://www.chromium.org/)项目还未提供编译出完整的Android版Chrome的途径，所以仅仅编出了简化版的外壳程序ChromeShell.apk。在这篇文章中，我介绍如何将ChromeShell从整个Chromium项目中抽取出来，从而方便对其定制。

<!--more-->

ChromeShell是简化版的Chrome，可以在其基础上定制以实现自己的浏览器。ChromeShell的所有代码都在Chromium项目中，因此定制的方式可以是直接对Chromium代码进行修改，然后重新编译。
但对于Chromium这样的大项目，其源码繁多，编译和维护都非常不方便。我们希望能把其中的ChromeShell抽取出来，构建成一个独立的工程。相比整个Chromium，这样一个独立工程就平易近人得多了。

抽取的关键在于弄清并取出ChromeShell所依赖的模块。这些模块在新工程里的存在形式可以是源代码，也可以是编译好的二进制库，取决于对该模块是否有定制的需求。一般而言，内核和底层的模块是不需要动的，需要改的是外壳的功能和界面。因此，本文所抽取出来的工程中仅保留了外壳的源代码，其它模块都是以库的形式存在。

下面详细介绍抽取的方法。

首先，我们需要先成功编译Chromium生成ChromeShell.apk。参考[编译指南](https://code.google.com/p/chromium/wiki/AndroidBuildInstructions)，确保其中的下述命令顺利执行完毕：

	ninja -C out/Release chrome_shell_apk
	
假设Chromium源码存放在/chromium/src，编译成功后应该在/chromium/src/out/Release中生成了我们需要的东西。

然后，执行下面所列的抽取操作。

### 拷贝src, res, 和AndroidManifest.xml

新建一个文件夹/chromeshell/ChromeShell来存放抽取后的工程。拷贝/chromium/src/chrome/android/shell/java/src/为/chromeshell/ChromeShell/src，拷贝/chromium/src/chrome/android/shell/res/为/chromeshell/ChromeShell/res/，拷贝/chromium/src/out/Release/gen/chrome\_shell\_manifest/AndroidManifest.xml为chromeshell/ChromeShell/AndroidManifest.xml。

### 创建Eclipse工程

在安装了Android开发工具（ADT）的Eclipse中, 使用菜单File->Import...->Android->Existing Android Code Into Workspace，选择/chromeshell/ChromeShell，通过导入已有代码创建一个新的Android工程。
确保在工程属性中target=android-21或更高。

### 准备原生库

原生库就是C++代码编译出来的库，在Android系统中通过JNI被Java层调用。我们可以从/chromium/src/out/Release中找到这些库，但更方便的是直接从编出来的APK文件（/chromium/src/out/Release/apks/ChromeShell.apk）里抽取。
将该APK文件解压缩（APK其实是zip压缩包），得到其中的原生库libchromeshell.so和libchromium\_android\_linker.so。把它们作为预编译库使用，准备好chromeshell/ChromeShell/jni/文件夹（参考Android NDK的用法）。

此外，为了使原生库被Java层加载，还需拷贝chromium/src/out/Release/chrome\_shell\_apk/native\_libraries\_java/NativeLibraries.java为chromeshell/ChromeShell/src/org/chromium/base/library\_loader/NativeLibraries.java。

### 准备assets

从解压的APK文件中拷贝出assets文件夹，直接放在/chromeshell/ChromeShell/下面即可。

### 准备库工程（Library projects）

ChromeShell依赖的一些模块不仅有Java代码，还有自己的资源文件（res），因此只能以库工程的方式使用它们。这些模块可以根据/chromium/src/out/Release/res.java/来确定。对于每一个有资源文件的库，从上述文件夹获取其资源，从/chromium/src/out/Release/lib.java/中获取对应的jar包，以此构建一个库工程。这些库工程的Java源码都封装在jar文件里了，所以它们的src文件夹为空（也可以干脆没有src）。

这些库工程之间也会有依赖关系，但它们最终都是为ChromeShell提供依赖。

### 添加其他Java库

新建文件夹/chromeshell/ChromeShell/libs/，将/chromium/src/out/Release/lib.java/中其他的jar文件（上一步没有涉及到的，也就是只有Java代码而没有资源文件的库）拷贝到其中。

还需要将/chromium/src/third\_party/android\_tools/sdk/extras/google/gcm/gcm-client/dist/gcm.jar拷贝到/chromeshell/ChromeShell/libs/，因为ChromeShell还依赖Android SDK中的这个库（FYI
：gcm = Google Cloud Message，用于实现Google云消息机制）。

### 按需添加Google Play Services模块

ChromeShell还依赖Android SDK中的Google Play Services库。但是，这个库太大了，如果我们将其整个导入新的工程，就会使新工程中的方法总数超过Android虚拟机允许的上限，就必须使用[Multidex](http://developer.android.com/tools/building/multidex.html)。然而Google有了新宠Android Studio后，干脆就不提供Eclipse支持Multidex的说明了，所以导入整个Play Services库很难在我们的Eclipse工程行得通。好在Play Services已经模块化，
我们的权宜之计就是只导入我们用得到的模块（参见<https://androiddevx.wordpress.com/2014/10/28/google-play-services-on-a-much-needed-diet/>）。这些模块包括：play-services-cast（是的，只有这一个；它是播放流媒体用的）。

本文并无意手把手进行指导，所以有些操作并没有给出详细的过程。但对于有一定经验的开发者，应该能据此得到一个独立的ChromeShell工程了。

Happy hacking！
