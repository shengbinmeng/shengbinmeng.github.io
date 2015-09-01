---
layout: post
title: 一些网络协议
date: 2014-09-06 17:00:00
category: 技术
tags: [网络]
---

最近了解了一些之前不知道的网络协议，总结在下面。

<!--more-->

##SCTP

SCTP全称是Stream Control Transmission Protocol，即流控制传输协议。它是一个传输层协议，实现的是与传统的TCP和UDP类似的功能。其特点主要有：

1. SCTP的连接可以是多主机的。在进行SCTP连接建立时，双方均可声明若干IP地址（IPv4，Ipv6或主机名）并通知对方。若当前连接失效，则协议可切换到另一个地址，而不需要重新建立连接。

2. SCTP是基于消息流的，即发送数据和应答数据的最小单位是消息包(chunk)；而且一个SCTP连接（Association）同时可以支持多个流(stream)，每个流包含一系列消息包。

3. 在网络安全方面，SCTP增加了防止恶意攻击的措施。不同于TCP连接采用的三次握手机制，SCTP连接采用四次握手机制，有效地防止了类似于SYN Flooding的拒绝服务（DoS）攻击。

##DTLS

了解DTLS先要了解TLS。TLS是传输层安全（Transport Layer Security）的简称，目的是在传输层协议上增加安全机制。我们平时上网遇到的https网址，就是表明该网站使用了传输层安全TLS（或者它的前任技术Secure Sockets Layer，SSL）。常用的传输层协议有TCP和UDP两种，而TLS只能用于TCP。随着越来越多的UDP应用也产生了安全传输的需求，TLS也开始向UDP应用移植。DTLS（Datagram TLS）便是扩展TLS框架使之支持UDP的结果。

例如，RTP是典型的基于UDP的应用。为了给RTP传输增加安全性，SRTP（Secure RTP）作为RTP的一个profile被开发出来。SRTP中的密钥管理、参数协商等过程就可以用DTLS实现（参见DTLS-SRTP的[rfc文档](http://tools.ietf.org/html/rfc5764)）。

##XMPP

XMPP全称是Extensible Messaging and Presence Protocol，即可扩展通信和表示协议。它是一种即时通信协议，主要是实现文字聊天。XMPP的前身是Jabber，一个开源的即时通信协议。Jabber被IETF标准化为XMPP。Google Talk用的就是它。

XMPP传输的数据是XML格式的。客户端或服务器发送的所有文本连缀在一起构成了从`<stream>`到`</stream>`的一个完整XML文档。在`<stream>`与`</stream>`之间，可以有其他各种XML元素，例如用来协商流的安全连接的元素，以及用来传输具体内容的元素。用来传输具体内容的元素在[XMPP的RFC文档](http://tools.ietf.org/html/rfc3920)中被称为XML Stanza（XML节），是`<stream/>`下的一级元素。上述RFC文档里定义的了三种XML节：`<message/>`、`<presence/>`、`<iq/>`。顾名思义，`</message>`中包含的就是所传输的消息内容，如下面的例子所示：

    <message from='juliet@example.com'
        to='romeo@example.net'
        xml:lang='en'>
        <body>Art thou not Romeo, and a Montague?</body>
    </message>

XMPP是应用层协议，它通常运行在TCP上。

##SDP

SDP全称是Session Description Protocol，即会话描述协议。它跟传输没太大关系，更多地是提供一个描述多媒体会话的规范。

在SDP中，一个会话由一系列的域来描述。每个域占一行，格式为：`<character>=<value>`。`<character>`是一个字符，表示某个属性；`<value>`是一个字符串，表示该属性的值。每个`<value>`的格式由对应的`<character>`决定。
下面是部分SDP域的说明：

    v=  (protocol version number, currently only 0)
    o=  (originator and session identifier : username, id, version number, network address)
    s=  (session name : mandatory with at least one UTF-8-encoded character)
    i=* (session title or short information)
    u=* (URI of description)
    
SDP的应用比较广泛，RTP、RTSP、SIP等协议里都有它的身影。最近兴起的WebRTC，也用它作为信令格式。我个人感觉，采用一个字母这样简洁的表示方法，使SDP显得十分可爱，这也是它受欢迎的原因之一吧。

维基百科上有个对[互联网协议套件](http://en.wikipedia.org/wiki/Internet_protocol_suite)的介绍，有空可以多看看。当然，有事没事读读RFC也是一个不错的消遣。
