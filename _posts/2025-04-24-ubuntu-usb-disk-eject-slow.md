---
title: Ubuntu下U盘弹出很慢的问题
date: 2025-04-24 00:00:00
category: 技术
tags: [Linux]
---

最近在从Ubuntu电脑向U盘拷贝大文件时，遇到了一个问题。
文件管理器显示拷贝很快就完成了，但此时若要弹出U盘则会显示“正在将数据写入，不要拔掉设备”的通知。
如果忽略该通知再次点击弹出，就会显示“无法弹出，一项操作正在进行中”。
等一段时间之后，才会收到可以拔出设备的通知。
拷贝的文件越大，这一等待的时间就越长。

<!--more-->

## 原因

这一现象看上去就令人怀疑是缓存导致的。通过查一些资料，确认了原因果然如此：Linux系统在拷贝文件时，会先将文件写入内存（作为写缓存），然后再从内存写入磁盘。写缓存（又称为脏内存）的大小可以通过下面的变量来配置为特定字节数或是占系统总内存的比例。

```console
shengbin@Suma-W40P:~$ sysctl vm.dirty_bytes
vm.dirty_bytes = 0
shengbin@Suma-W40P:~$ sysctl vm.dirty_ratio
vm.dirty_ratio = 20
```

可以看到，Ubuntu系统将其默认配置为了总内存的20%。由于我的总内存有128G，写缓存就有25.6G。
当向U盘拷贝低于该大小的文件时，全部数据先被快速拷贝到了缓存里，然后才慢慢写入U盘（从缓存写入目标磁盘的操作是自动进行的，但也可以通过`sync`命令手动发起）。
而无论是命令行的`cp`还是图形界面的文件管理器，都报告的是拷贝到缓存里的速度和进度。
之后慢慢写入的过程就导致了需要等一段时间才能弹出U盘的现象。
拷贝的文件越大，U盘写入越慢，需要等的时间就越长，甚至会让人以为是卡住了。用户体验确实较差！

## 解决方法

要避免这一现象，我们可以完全禁用上述这种利用写缓存进行异步读写的机制，改为同步读写。不过这样会有两个负面影响，一是导致总拷贝耗时有所增加，二是过于频繁写入磁盘的话其使用寿命可能缩短。更好的方法是仍然采用缓存，但不要这么大。如果我们修改`vm.dirty_bytes`或`vm.dirty_ratio`的值，那所有磁盘写入都会受影响，所以最好的方法是只针对U盘来配置较小的写缓存。

好在，Linux从某个较新的内核开始，支持了这么做。只需在`/etc/udev/rules.d/`目录下新建一个规则文件`99-usb-sync.rules`，写进如下内容：

    ACTION=="add", KERNEL=="sd[a-z]", SUBSYSTEM=="block", ENV{ID_USB_TYPE}=="disk", RUN+="/usr/bin/bash -c 'echo 1 > /sys/block/%k/bdi/strict_limit; echo 16777216 > /sys/block/%k/bdi/max_bytes'"

这一规则会在有U盘插入时，通过`/sys/block/%k/bdi/max_bytes`为其配置最大写缓存。

上述改动在重启后生效。不重启的情况下也可以通过执行如下命令使其生效：

    sudo udevadm control --reload-rules && sudo udevadm trigger

## 结果

在进行上述改动前：

```console
shengbin@Suma-W40P:~$ time cp ~/Downloads/docker-desktop-amd64.deb /media/shengbin/TPU301/ && time sync

real    0m1.167s
user    0m0.001s
sys     0m1.165s

real    0m8.191s
user    0m0.000s
sys     0m0.004s
```

可以看到，`cp`命令1秒多就完成了，而之后从缓存实际写入磁盘的`sync`命令耗时却长达8秒多。

在进行上述改动后：

```console
shengbin@Suma-W40P:~$ time cp ~/Downloads/docker-desktop-amd64.deb /media/shengbin/TPU301/ && time sync

real    0m8.476s
user    0m0.003s
sys     0m0.878s

real    0m0.032s
user    0m0.001s
sys     0m0.003s
```

可见`cp`命令就真实反映了所有拷贝耗时。相应的，当图形界面的文件管理器告知说拷贝进度完成的时候，数据也确实已经全部写入了磁盘，此时弹出U盘就可以立即进行了。

其实，这一问题影响体验的地方也主要是图形界面用户。
[有网友提出](https://forum.manjaro.org/t/strict-limit-of-write-cache-0s-sync-time-policy-for-usb-devices-by-default/166934/3)，文件管理器的开发者应该在其内部调用`sync`命令来确保缓存的数据也全部写入之后再报告拷贝完成。
我认同这一观点，这样确实符合了普通用户的预期，也不需要进行本文所说的修改了。

