---
layout: post
title: 基于WebRTC的多人视频会议
date: 2014-07-25 17:00:00
category: 技术
tags: [Web, 开源]
---

最近两周在调研和搭建基于WebRTC的多人视频会议系统。目前已经搭建成功，可以在<http://jitsi.shengbin.me/>试用。

<!--more-->

这个系统无需注册和登录，只要多人访问同一个URL（含有系统为每个房间分配的特定ID），就可以进行视频会议。如果上面那个链接失效，可以尝试国外一个同样的系统：<https://meet.jit.si/>。使用视频会议系统需要客户端电脑提供摄像头功能；至于带宽，当然是越大越好了。

下面总结一下该系统的组成。

## 客户端

客户端是一个Web App的形式，包括HTML、CSS、JavaScript代码组成的网页。HTML和CSS来构造聊天室的界面，JavaScript来实现功能。由于功能比较复杂，JS代码也较多。

通过WebRTC，客户端从用户摄像头获取图像并传给服务器，来实现视频会议。由于WebRTC只在Chrome、Opera、Firefox上支持，而Firefox有[相关的bug](https://bugzilla.mozilla.org/show_bug.cgi?id=977864)尚未解决，所以客户端只能运行于Chrome或者Opera浏览器。

## 服务器

服务器端包含多个部分。下面分别介绍。

### [Nginx](http://nginx.org/)

Nginx是一个Web服务器，与著名的Apache同类。它的用途是提供网页访问。

### [Prosody](http://prosody.im/)

Prosody是一个XMPP服务器。XMPP全称是Extensible Messaging and Presence Protocol，即可扩展通信和表示协议。它是一种即时通信协议，主要是实现文字聊天。

XMPP的前身是Jabber，一个开源的即时通信协议。Jabber被IETF标准化为XMPP。Google Talk用的就是它。

### [Jitsi-Videobridge](https://jitsi.org/Projects/JitsiVideobridge)

Jitsi-Videobridge用于处理视频传输，也就是视频流在各参与者之间的转发。如果没有这个组件，各参与者能文字聊天，但无法互相看见。

转发意味着服务器要从N个参与者那里接受视频流，然后给每个参与者发送其他N-1个参与者的视频数据，这对服务器带宽要求很高。但由于未对视频做任何处理，CPU负载并不高。

### [Restund](http://www.creytiv.com/restund.html)

这是一个STUN/TURN服务器。STUN是一种NAT穿透技术，用于帮助处在内网的主机确定自己的公网IP和端口，从而与别的主机建立直接连接（WebRTC中PeerConnection）。TURN是STUN的增强版，可以在无法穿透NAT进行直连的情况下提供数据的转发。

上述整个系统都是开源的，更多信息可参见相关的[GitHub代码库](https://github.com/jitsi/jitsi-meet)和[Jitsi主页](https://jitsi.org/)。
