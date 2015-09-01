---
layout: post
title: 计算机体系结构、ARM和苹果A8
date: 2014-09-12 19:00:00
category: 技术
tags: [Apple, 硬件, ARM]
---

最近苹果公司发布了新手机iPhone 6/iPhone 6 Plus，里面用的是苹果A8。本文是一篇由此引发的大杂烩。要说的内容有三部分，依次是计算机体系结构、ARM和苹果A系列芯片。

<!--more-->

计算机体系结构（Computer Architecture）是对计算机系统的各个部分及其相互关系的描述。计算机架构师或者设计师们的目的是提出新的计算机体系结构，也就是设计和实现新的计算机。

计算机体系结构包含的内容可以分为以下几个部分。

* 指令集架构（Instruction Set Architecture，ISA）。ISA主要是定义CPU执行的指令，包括操作符和操作数（构成机器语言或汇编语言的基础）。ISA涉及的内容还有机器字长、寄存器数目和类型、寻址模式等。

* 计算机组织（Computer Organization）。计算机组织又称为微结构（Microarchitecture），是围绕处理器的一些核心设计，例如流水线、缓存、多核并行、内存访问等等。

* 硬件实现（Hardware Implementation）。这就具体到工程细节了，如时钟、电路、封装等，以及其他构成计算机的部件。

ARM，通常指的是ARM公司设计的一种指令集架构，即属于上面说的ISA。ARM属于精简指令集，与其相对应的是复杂指令集的x86架构。ARM指令集版本在不断更新，从最早的ARMv1发展到最新的ARMv8。近些年的版本还根据不同的性能需求分为了不同的档次（profile），如ARMv7分为ARMv7-A（Application，普通应用）、ARMv7-R（Realtime，实时）、ARMv7-M（Microcontroller，微控制器）。ARM架构的处理器应用非常广泛，尤其是在移动设备和控制器中，几乎全是ARM指令在执行。

ARM公司除了设计并授权ARM指令集架构，其主要的一项生意是设计并授权IP核。其他厂商在IP核的基础上生产CPU，可以认为IP核是CPU的雏形。ARM公司针对其每一版本的指令集都设计了相应的IP核，有的版本还有多种IP核。例如，ARMv3指令集对应的IP核有ARM6和ARM7。从ARMv7开始，ARM公司开始将它的IP核命名为Cortex系列。例如，ARMv7-R指令集对应的IP核有Cortex-R4、Cortex-R5等等，而ARMv7-A指令集对应的IP核有Cortex-A5、Cortex-A8、Cortex-A9、Cortex-A15等等。很多产商都从ARM公司获取IP核的授权，然后形成自己的CPU，并且将CPU、GPU和其他单元一起，组成片上系统（System on Chip，SoC）。

苹果公司的A系列芯片，就是这样的片上系统。iPhone 4用的苹果A4芯片，上面集成的是ARM Cortex-A8 CPU；iPhone 4s使用的是A5芯片升级为了双核ARM Cortex-A9 CPU。从iPhone 5上的A6芯片开始，苹果公司决定不再使用ARM公司的IP核，而是自己设计IP核。A6上的CPU（IP核）叫做Swift（没错，确实跟苹果公司新推出的编程语言同名）。Swift的指令集架构是苹果自己基于ARMv7改的ARMv7s，而在设计上则借鉴了ARM Cortex-A15的一些特性。iPhone 5s上的A7芯片，则在处理器上又垮了一大步。这时候ARM公司的指令集版本已经更新到ARMv8，而这个版本的应用档次ARMv8-A是64位的！苹果公司紧随潮流，基于ARMv8-A设计了64位CPU（名为Cyclone），并将其用在A7芯片上，随iPhone 5s一起推出。iPhone 5s的指纹识别和64位处理器在手机领域都是开创性的，使其成为当之无愧的领军设备。

苹果A8芯片，是苹果公司最近这几天刚发布的iPhone 6/iPhone 6 Plus这两款手机使用的芯片。A8相比A7，CPU没有换代（用的还是Cyclone），只是在制造工艺上有更新。另外，A8的GPU也用了新的型号，使得图形处理能力提高了50%。毕竟，iPhone 6和iPhone 6 Plus主要以大屏著称嘛！


