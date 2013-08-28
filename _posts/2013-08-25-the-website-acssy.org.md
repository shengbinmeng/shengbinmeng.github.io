---
layout: post
title: 网站 acssy.org
date: 2013-08-25 22:00:0
category: 技术
tags: [Web]
---
{% include JB/setup %}

我暑假期间接手了一个为别人制作网站的项目。该网站属于耶鲁大学中国学生学者联合会，网址是<http://acssy.org>。
网站后台基于WordPress，前端基于某个主题做了样式和页面的修改。目前该项目已经完成。以下是为网站管理者写的使用说明。

<!--more-->
###登陆管理后台
使用用户名和密码在<http://acssy.org/wp-admin>登录网站后台。网站内容主要以Pages和Posts这两种形式呈现。后台管理也主要是管理这两部分内容。下图标注出了这两个链接。

<!-- [![](/images/2013-08-25-posts-and-pages.png)](/images/2013-08-25-posts-and-pages.png "点击看大图") -->
<a href="/images/2013-08-25-posts-and-pages.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-posts-and-pages.png" wdith="437px"/></a>

###Pages（页面）
首先点击Pages，得到如下图所示的Page列表。

<a href="/images/2013-08-25-page-list.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-page-list.png" width="611px" /></a>

Page是网站中相对比较固定的内容。网站首页的Tab菜单，里面的各项都对应一个个的Page（页面）。页面之间有父子关系，同时也对应了菜单与（下拉）子菜单的关系。
上图中，About Us是一级页面，对应Tab上的一级菜单；ACSSY Bylaw和Board Members是其子页面，对应该Tab的下拉菜单。

网站的Pages确定后，一般不再经常增删或修改。如确要修改某个页面，点击上图中页面列表中的链接进入修改。例如，点击“ACSSY Bylaw”后进入下图所示界面。

<a href="/images/2013-08-25-edit-page.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-edit-page.png" /></a>

上图中A处的Add New用于添加新页面，点击后进入与上图类似的编辑界面。在上图中C处选择Visual，即可在所见即所得的编辑区进行编辑，
使用工具栏调整格式（如果略懂HTML和CSS，可在C处选择Text进行编辑）。在上图中D处的页面属性中指定各个页面的父子关系。
上图中B处的Add Media用于添加图片等多媒体材料，点击后界面如下图，可上传媒体文件。

<a href="/images/2013-08-25-upload-media.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-upload-media.png" /></a>

###Posts（帖子）
网站内容的更新大多是以Posts（帖子）的形式，平时的管理操作也主要是针对这个。网站的帖子分为三个类别（Category）：News、Upcoming Events、Featured。
前两类对应首页右下角的两栏，最后一类对应首页以动画图片展示的特别内容。一个帖子可以同时属于多个类别，但根据网站逻辑，一般不让帖子同属于News和Upcoming Events这两个类别。

发布一个帖子就是在后台的Posts管理页面新建一个Post。Post的添加和编辑与Page类似，如下图。

<a href="/images/2013-08-25-edit-post.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-edit-post.png" /></a>

图中B处用于指定帖子的类别。新建的帖子默认属于Upcoming Events。如果某事件结束后想要将其移至News栏目，只需将其所属分类改为News即可（帖子内容措辞可能也需修改）。

要想将帖子里的图片在首页动画中显示，首先为该帖子增加Featured分类；其次，在图中A处增加一个名为articleimg的Custom Field，将其值设置为图片的URL地址。

###网站首页
上面已经提到，网站首页的Tab菜单、动态图片、右下角的列表都取决于Pages和Posts的管理。首页左侧栏的内容在home.php文件中实现，如要更改需编辑该文件。
下面说明如何更改首页左侧栏的Sponsors。

登陆管理后台，点击Appearance->Editor进入后台文件的编辑，如下图。

<a href="/images/2013-08-25-editor.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-editor.png" width="321px" /></a>

Editor的界面如下图所示。点击下图中A处开始编辑home.php。

<a href="/images/2013-08-25-edit-home.png" title="点击看大图" target="_blank">
<img src="/images/2013-08-25-edit-home.png" /></a>

Sponsors对应上图中B处的代码。方框中是一个Sponsor。如要添加，紧接着其下方复制这段代码，并将其中的链接更新。
代码中src=的是图标的地址，href=的是点击Logo后跳转到的商家的链接。

###最后
若遇到本文未说明的问题，向略懂Web技术的人寻求帮助即可。