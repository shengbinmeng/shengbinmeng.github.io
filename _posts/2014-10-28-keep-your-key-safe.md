---
layout: post
title: 保存好你的Key！
date: 2014-10-28 19:00:00
category: 技术
tags: [Android, Google, Java]
---
{% include JB/setup %}

吃一堑，长一智。

<!--more-->

今天本打算将Google Play Store上的安卓版[IPGateway](http://www.shengbin.me/apps/ipgateway/)升级一下。可当我上传APK的时候，却遇到了错误。提示信息说我为新版本签名的Key跟已有的版本不一致。

我想了想，确实不是同一个Key。由于安卓程序签名的Key是开发者自己创建的，我在换电脑的时候就懒得拷贝而重新生成了一个。当时没觉得有什么不妥，可现在问题来了：Google Play应用商店要求一个应用的各版本都必须用同一个Key来签名。我赶紧设法寻找之前用的那个Key，但结果怎么也找不到了（应该是在哪次清理回收站的时候给彻底删除了）。目前来看，我再也无法给我在应用商店里的IPGateway更新了。

这就是一个悲剧！

除了为此而悲伤之外，至少还有三点值得反思：

第一，Google允许开发者自己随便生成任意多个Key是不是有点太放任了？俗话说，来的容易就不会太珍惜。现在真怀念我之前的那个Key啊！

第二，数据备份和文件历史记录真的很重要。如果忽略之，不出事便罢，一出事则后悔莫及。是时候下决心搞起File History和Time Machine了。

第三，Google的开发者[文档](https://developer.android.com/tools/publishing/app-signing.html#overview)里专门标红强调了保存好Key的重要性，可是我没好好读。这再次验证了文档不应只在遇到问题时才去查阅。有事没事都去看看多好。

Android版IPGateway的用户可以从百度网盘下载新版本的[APK文件](http://pan.baidu.com/s/1kT3L1R5)自行安装。如果你是开发者，请吸取我的教训，保存好你的Key！
