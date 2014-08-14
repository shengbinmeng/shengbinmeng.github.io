---
layout: post
title: WebRTC简介
date: 2014-06-21 17:00:00
category: 技术
tags: [Web]
---
{% include JB/setup %}

WebRTC全称是Web Real Time Communication，即网络实时通信。这是一个正在制定中的Web标准，其目的是使网络浏览器之间在不依赖别的插件的前提下能够进行实时的多媒体（视频和音频）通信。

<!--more-->

以视频聊天为例，在WebRTC之前，通过浏览器进行视频聊天都需要安装插件。例如，使用Google Hangouts需下载安装Google Talk plugin。有了WebRTC，浏览器本身就具备了让两个终端进行实时通信的能力，用户不需要额外的插件就可以视频聊天。目前支持WebRTC的浏览器有Chrome、Firefox和Opera。

下面，我们介绍WebRTC涉及的几个主要部分。

## getUserMedia

这个是获取用户终端媒体流的JavaScript函数。示例代码如下：

{% highlight javascript%}
<video autoplay></video>

<script>
  var errorCallback = function(e) {
    console.log('Rejected!', e);
  };

  var successCallback = function(localMediaStream) {
    var video = document.querySelector('video');
    video.src = window.URL.createObjectURL(localMediaStream);
  };
  
  var constrains = {audio: true, video: true};
  
  navigator.getUserMedia(constrains, successCallback, errorCallback);
</script>
{% endhighlight %}

`getUserMedia`接收三个参数，第一个用于指定要获取媒体的约束条件，第二个是获取成功时的回调函数，第三个是出错时的回调函数。
浏览器执行这个函数时会向用户请求允许使用音视频硬件。如果请求失败（用户拒绝或是其他原因），则转而错误回调函数；否则执行成功回调函数。
成功回调函数会被传入一个参数，这个参数是一个`MediaStream`类型的变量，通过它就可以使用用户媒体。

`getUserMedia`得到的用户媒体不一定要用于远程通信，完全可以仅在本地使用（例如自拍）。因此这个接口在WebRTC之前就在HTML5标准中出现了。更多信息可以参考HTML5Rocks网站上对它的[介绍](http://www.html5rocks.com/en/tutorials/getusermedia/intro/)。

## RTCPeerConnection

RTCPeerConnection是两个终端进行远程通信需要用到的API。上面介绍的getUserMedia得到的MediaStream通常作为RTCPeerConnection的输入或输出。

## RTCDataChannel

RTCDataChannel可以用来传输任意的数据，不仅限于MediaStream所表示的音视频媒体。

最后附上我给别人简单介绍WebRTC时用的PPT，其中有RTCPeerConnection和RTCDataChannel的具体函数和一些参考链接。

<iframe src="https://onedrive.live.com/embed?cid=8B504C1595CD3973&resid=8B504C1595CD3973%2127643&authkey=AIP8GTYzfq9pIHY&em=2" width="700" height="500" frameborder="0" scrolling="no"></iframe>
