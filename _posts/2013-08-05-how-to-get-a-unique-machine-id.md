---
layout: post
title: 如何获取唯一的机器码
date: 2013-08-05 22:00:0
category: 技术
tags: [Linux, Windows, 编程]
---
{% include JB/setup %}

获取唯一的机器码是采用密钥对软件进行保护的基础。一般来说，一台电脑的主要部件都会有一个序列号或是其他唯一的ID，唯一的机器码可以用这些ID组合、变换（例如进行md5）得到。因此，获取唯一的机器码归结为如何获取机器主要部件的属性。下面介绍在Windows下和在Linux下该如何做（重点是Linux）。

<!--more-->

##Windows

Windows系统提供了一个被称为[WMI](http://en.wikipedia.org/wiki/Windows_Management_Instrumentation)的机制，可用于管理各个部件。
我们可以借助它来向操作系统查询各个设备的属性，十分方便。具体的调用借口可以看MSDN文档，或上网搜索，这里就不详细介绍了。

##Linux

Linux下没有类似WMI那样由系统提供的查询接口，程序员们必须自己动手。Linux系统是基于文件的，它将硬件设备也映射为文件。
因此，Linux下主机的硬件信息一般都可以在文件系统中读取。例如，主板的序列号可以通过读取`/sys/class/dmi/id/board_serial`
这个文件得到：

	char buffer[128];
	FILE *file = fopen("/sys/class/dmi/id/board_serial", "r");
	if (file != NULL) {
		fgets(buffer, 128, file);
	}
	
需要注意的是，不同的Linux发行版系统文件目录结构可能不完全一样，像上面那样直接读某个绝对路径可能会不成功。这就要分情况进行尝试了。
而且不同的硬件类型对应的文件也不同，例如如果机器采用SATA的硬盘，则磁盘信息在`/dev/sda`中；如果是IDE硬盘，则是`/dev/hda`。

Linux下有一个工具叫做dmidecode，在终端中执行它可以打印出机器硬件信息。在程序代码中，可以使用pipe来读取终端命令的结果，这就意味着能够通过dmidecode来获取某些机器属性。例如，读取以下命令的结果可以得到处理器ID：

    sudo dmidecode 2>&1 -t 4 | grep ID:

最后指出，上述Linux下获取硬件属性的方法大多需要root权限，这可能对某些程序来说比较尴尬，请自行权衡利弊。
