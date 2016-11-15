---
title: NTIA移交IANA职能
date: 2016-09-24 12:00:00
category: 技术
tags: [DNS]
---

题目说的是：美国商务部下设的国家电信和信息管理局（NTIA）不再行使互联网资源分配方面的职能（IANA职能）。

<!--more-->

## IANA职能

[IANA](https://www.iana.org/)全称是Internet Assigned Numbers Authority，即互联网数字分配机构。它负责互联网资源的协调管理，其职能主要包括以下三方面：

* 域名系统（DNS）根区管理。根区是域名系统的最顶层部分，所有顶级域名都属于根区。
* 互联网号码资源分配。简单来说就是所有IP地址的分配（其实IP地址只是网络地址中的一种）。
* 协议参数指定。确定各种网络协议中涉及的数字值。比如Web服务器默认是80端口，HTTP中用404表示页面未找到，其中的“80”，“404”就是所谓的协议参数。

IANA最早可能是一个组织机构，但现在人们说起IANA，都是对上述职能的统称，并不指代某一个组织。事实上，IANA职能的行使方也并不是一个固定的组织。

## IANA职能的行使方

IANA职能最早是由参与ARPANET（互联网的前身）研究的人员和单位来行使。这些人员和单位都受美国国防部（ARPANET的发起和资助方）的合同约束，因此，此时IANA职能实质上是由美国政府来行使的。
随着互联网的规模扩大及商业化，美国开始着手将IANA职能从政府剥离出去，进行私有化。
这工作[由美国商务部牵头实施](https://www.icann.org/resources/unthemed-pages/white-paper-2012-02-25-en)。
商务部下设的国家电信和信息管理局（National Telecommunications and Information Administration，简称NTIA）提议建立一个非营利性组织来行使IANA职能。
这就是互联网名称与数字地址分配机构，即Internet Corporation for Assigned Names and Numbers，简称[ICANN](https://www.icann.org/)。

ICANN虽然不是政府部门，但它仍然在政府的合同约束下行使IANA职能，只不过合同的甲方由美国国防部换成了美国商务部（具体来说是其下设的NTIA）。
随着私有化工作的进展，NTIA对号码资源分配和协议参数指定这些更偏技术的方面不再主动参与了。但是对于域名系统的管理，NTIA却始终把握着实权。
这体现在NTIA[在合同中扮演的角色](https://www.icann.org/en/system/files/files/sac-068-en.pdf)：
对于号码资源和协议参数相关的职能，NTIA只作为监督方；而对于域名根区管理，NTIA还是具体实施者，也就是说根区内容的任何变动都要由它授权。

## NTIA退出

NTIA决定在这届合同到期（今年9月31日）之后，就不再继续参与IANA职能的行使了，要将其完全移交出去。

关于这次移交的详细信息可以参见：<https://www.icann.org/stewardship>。
