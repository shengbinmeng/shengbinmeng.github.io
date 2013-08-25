---
layout: post
title: 网站 accsy.org
date: 2013-08-25 22:00:0
category: 技术
tags: [Web]
---
{% include JB/setup %}

我暑假期间接手了一个为别人制作网站的项目。该网站属于耶鲁大学中国学生学者联合会，网址是<http://acssy.org>。网站后台基于WordPress，基本不用动；需要做的工作主要是修改样式和部分前端页面。目前该项目已经基本完成。以下是为网站使用者写的说明要点。

<!--more-->
使用用户名和密码在<http://acssy.org/wp-admin>登录网站后台。

在网站发布内容主要用到Posts和Pages这两种形式。Pages是相对比较固定的页面，例如网站首页的Tab菜单，里面的各项都对应一个个的Page（页面）。网站平时的使用一般不需要涉及Pages，除非有固定的内容页面需要增加或修改。

网站内容的更新大多是以Posts的形式，平时的操作也主要是这个。目前网站的Posts分为三个类别（Category）：News、Upcoming Events、Featured。前两类对应首页右下角的两栏，最后一类对应首页以动画图片展示的特别内容。一个Post可以属于多个类别。

发布一个帖子就是在后台的Posts管理页面新建一个Post。新建的Post默认属于Upcoming Events。如果事件结束后想要将其移至News栏目，只需要编辑对应的Post，将其所属分类改为News即可。如果想将一个帖子里的图片在首页动画中显示，为其增加Featured属性即可。

如果想只在首页动画里增加一个图片，新建一个只有Featured分类的帖子，将图片作为其内容即可。
