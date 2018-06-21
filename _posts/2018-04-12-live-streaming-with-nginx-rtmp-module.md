---
title: 直播系统
date: 2018-04-12 12:00:00
category: 技术
tags: [DASH, FFmpeg]
---

最近搭建了一个简单的直播系统，可以支持RTMP、HLS、DASH等多种协议，下面描述一下具体方法。这个例子是在macOS上进行的，但对于其他操作系统也是类似的。

<!--more-->

首先搭建一个接受推流的RTMP服务端。我们采用带有[RTMP功能模块的Nginx](https://github.com/arut/nginx-rtmp-module)。这是开源软件，我们可以从源码编译。
在macOS上，也可以方便地通过Homebrew来安装：

	brew denji/nginx
	brew install nginx-full --with-rtmp-module

安装成功后，在配置文件`nginx.conf`里加入RTMP服务信息，以及提供HLS和DASH访问所需的HTTP服务信息：

	http {
		server {
			listen 80;
			location /hls {
				# Serve HLS fragments
				types {
					application/vnd.apple.mpegurl m3u8;
					video/mp2t ts;
				}
				root /tmp;
				add_header Cache-Control no-cache;
			}

			location /dash {
				# Serve DASH fragments
				root /tmp;
				add_header Cache-Control no-cache;
			}
		}
	}
	
	rtmp {
		server {
			listen 1935;
			# Receive and then serve RTMP stream
			application rtmplive {
				live on;
			}
			# Receive RTMP stream and transmux to HLS
			application hls {
				live on;
				hls on;
				hls_path /tmp/hls;
			}
			# Receive RTMP stream and transmux to DASH
			application dash {
				live on;
				dash on;
				dash_path /tmp/dash;
			}
		}
	}
	
这里面的rtmp server含有三个application：rtmplive、hls、dash。这意味着该RTMP服务器（假设运行在localhost）可以接收向以下三个地址推流：
`rtmp://localhost/rtmplive/<stream_name>`，`rtmp://localhost/hls/<stream_name>`，`rtmp://localhost/dash/<stream_name>`。
其中`<stream_name>`为流的名字，可由推流客户端指定。
当接收到向rtmplive推的流时，该服务器提供中转功能，使其他播放客户端能从同一地址拉流播放。
当接收到向hls推的流时，该服务器不仅提供上述中转功能，还会把接收到的流切片为HLS格式，存放在指定的临时路径。
由于我们在http server里对该临时路径配置了提供HTTP服务，播放客户端便能从`http://localhost/hls/<stream_name>.m3u8`来按HLS播放了。
DASH和HLS是一样的道理（其播放地址为`http://localhost/dash/<stream_name>.mpd`）。

作为演示，推流客户端我们直接用FFmpeg来模拟。下面的命令把macOS的电脑屏幕作为直播视频源推流给RTMP服务器：

	ffmpeg -f avfoundation -i "0" -s 640x360 -c:v libx264 -f flv rtmp://localhost/rtmplive/screen
	
播放客户端即可播放同一地址：

	ffplay rtmp://localhost/rtmplive/screen

下面是一个把视频文件作为数据源并提供DASH播放的例子：
	
	ffmpeg -re -i movie.mp4 -vcodec libx264 -f flv rtmp://localhost/dash/movie
	
播放：

	ffplay http://localhost/dash/movie.mpd
	
对于HLS和DASH，也可以不使用RTMP服务的切片功能，而是用rtmp流作为输入来自行切片，例如：

	ffmpeg -i rtmp://localhost/rtmplive/screen -f dash out.mpd
	
实际上，可以在`nginx.conf`中的每个application下直接配置执行外部命令，来对推向这个application地址的流做自定义处理。具体可参见[示例](https://github.com/arut/nginx-rtmp-module/blob/master/README.md#example-nginxconf)。