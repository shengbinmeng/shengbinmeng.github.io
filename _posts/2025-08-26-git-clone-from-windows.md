---
title: 从Windows克隆Git仓库
date: 2025-08-26 00:00:00
category: 技术
tags: [Windows,Git]
---

Windows不如Linux更适合开发者。当我试图从Windows上克隆一个Git仓库时再次深刻体会到了这一点。

<!--more-->

如果Git仓库是在Linux系统上，例如位于`/projects-path/my-repo`，我们可以直接通过加上远端机器地址前缀（host）来将它克隆下来：

    git clone host:/projects-path/my-repo

和从本地目录克隆一样简单。

不过我面临的情况下，仓库位于另一台Windows机器上（`Z:\projects-path\my-repo`），上述命令就没那么容易成功了。

首先，Windows默认是没有开启SSH服务的，需要操作一番。这包括安装这一可选功能、启动服务、设置为自启动。
好在启动SSH服务时Windows系统会自动增加防火墙规则来允许22端口的通信。

如果你像我一样，曾怀疑连接到Windows机器的网络有问题，想通过ping命令测试一下，那就容易被误导。
默认情况下是ping不通的，并非因为网络不通，而是因为Windows防火墙禁用了ping命令所依赖的ICMP协议。
这在Linux上理所当然能用的功能在Windows上还要手动配置一下。

接下来，Git能通过SSH访问Windows了，但执行`git clone host:/Z:/projects-path/my-repo`总是报如下错误：

    fatal: ''/Z:/projects-path/my-repo'' does not appear to be a git repository

看到这一报错第一感觉是路径形式没写对。
可是试了把`/Z:/projects-path/my-repo`换为`Z:/projects-path/my-repo`、`/Z/projects-path/my-repo`等多种形式都报错，斜杠换成反斜杠也不行。

查到网上[一个帖子](https://superuser.com/a/1841390/3112705)才总算弄清楚了，原来是Windows的SSH服务默认使用的终端Cmd.exe存在路径兼容性问题。
这时也才注意到，上面报错中有两个引号，Cmd.exe把`'/Z:/projects-path/my-repo'`（包括引号）当作路径去使用了。
按照该帖子的方法替换Git内部工具（git-upload-pack）果然就可以成功克隆了。

在替换Git内部工具的过程中，需要修改`PATH`环境变量。为使修改生效，SSH服务也要重启。此外，对于SSH服务使用的环境变量，不能用`%USERPROFILE%`代替用户目录（我干脆直接写了绝对路径），因为会被替换为另一个配置项……这都是操作过程中踩出的坑啊。

最后虽然达到了目的，但我总觉得魔改Git内部工具的方法有点不优雅。
既然是Cmd.exe的兼容性导致的问题，那是不是将SSH服务的终端换为PowerShell也能解决呢？
这个方法经验证也是可行的。
不过要替换SSH服务所用的终端呢，就只能通过Windows代表性手段——修改注册表——来实现了！
具体参见我在[那个帖子](https://superuser.com/a/1841390/3112705)下面评论里做的补充。

