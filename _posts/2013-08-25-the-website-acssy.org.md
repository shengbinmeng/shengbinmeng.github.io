---
layout: post
title: 网站 accsy.org
date: 2013-08-25 22:00:0
category: 技术
tags: [Web]
---
{% include JB/setup %}

我暑假期间接手了一个为别人制作网站的项目。该网站属于耶鲁大学中国学生学者联合会，网址是<http://acssy.org>。网站后台基于WordPress，基本不用动；需要做的工作主要是修改样式和部分前端页面。目前该项目已经基本完成。以下是为网站使用者写的使用说明。

<!--more-->
使用用户名和密码在<http://acssy.org/wp-admin>登录网站后台。网站内容主要以Pages和Posts这两种形式呈现。后台管理也主要是管理这两部分内容。下图标注出了这两个链接。

![](/images/2013-08-28-posts-and-pages.png)

首先点击Pages，得到如下图所示的Page列表。

![](/images/2013-08-28-page-list.png)

Page是相对比较固定的页面，例如网站首页的Tab菜单，里面的各项都对应一个个的Page（页面）。页面之间有父子关系，同时也对应了菜单与（下拉）子菜单的关系。上图中，About Us是一级页面，对应Tab上一级菜单；
ACSSY Bylaw和Board Members是其子页面，对应该Tab的下拉菜单。

网站的Pages确定后，一般不再经常增删或修改。如确要修改某个页面，点击上图中页面列表中的链接进入修改。如下图。

![](/images/2013-08-28-edit-page.png)

图中A处的Add New用于添加页面，点击后进入与上图类似的编辑页面。在图中C处选择Visual，即可在所见即所得的编辑区进行编辑，使用工具栏调整格式（如果略懂HTML和CSS，可在C处选择Text进行编辑）。在上图中D处的页面属性中指定各个页面的父子关系。图中B处的Add Media用于添加图片等多媒体材料，点击后界面如下图，可上传媒体文件。

![](/images/2013-08-28-upload-media.png)

网站内容的更新大多是以Posts（帖子）的形式，平时的操作也主要是这个。网站的帖子分为三个类别（Category）：News、Upcoming Events、Featured。前两类对应首页右下角的两栏，最后一类对应首页以动画图片展示的特别内容。一个帖子可以同时属于多个类别，但根据网站逻辑，一般不让帖子同属于News和Upcoming Events这两个类别。

发布一个帖子就是在后台的Posts管理页面新建一个Post。帖子的添加和编辑与页面类似，如下图。

![](/images/2013-08-28-edit-post.png)

图中B处用于指定帖子的类别。新建的帖子默认属于Upcoming Events。如果某事件结束后想要将其移至News栏目，只需将其所属分类改为News即可（帖子内容措辞可能也需修改）。

要想将帖子里的图片在首页动画中显示，首先为该帖子增加Featured分类；其次，在图中A处增加一个名为articleimg的Custom Field，将其值设置为图片的URL地址。

