---
title: Git分支管理
date: 2016-08-10 12:00:00
category: 技术
tags: [Git]
---

一个项目用Git进行版本控制时，应该进行合适的分支管理，这样才能保证版本库的高效使用。

<!--more-->

## 总体原则

主分支master上是产品代码，应能直接编译和使用。分支develop上是开发过程中供集成测试的代码。其他分支上的提交均合并到develop；develop上的提交在测试没问题后合并至master。
有的项目会在develop和master之间增加一个release分支，用来做产品化准备。

总之分支是按照不同的代码稳定性程度来管理的。

## 开发流程

开发者A实现新的特性时需另开相应的实现分支（如feature1、topic2），在此分支上进行实现提交之后，向develop发起合并请求。

负责人B审核实现分支上的提交，将它们合并到develop。如果A比较有经验，A和B可以是同一个人。

每次有提交被合并到develop之后应看该分支能否正常工作（主要考虑各个开发者实现模块间的配合并确保没问题）；如果不行的话，A和B在develop上添加修改提交，使之能够正常工作。

在develop上的代码能正常工作的前提下，负责人C决定何时将develop上的提交合并到master来升级产品。B和C可以是同一个人。

如果实现分支需继续开发，就将develop上的修改提交（若有的话）合并过来，然后再继续进行实现提交，并重复上述过程。

如果实现分支完成了，应将其删除以保持版本库整洁。

各个实现分支可以随时从develop或master拉取提交，以使自己尽量与主线同步。

## 参考

<http://nvie.com/posts/a-successful-git-branching-model/>

<https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows>