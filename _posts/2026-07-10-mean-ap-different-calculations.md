---
title: mAP的不同计算方式
date: 2026-07-10 00:00:00
category: 技术
tags: [算法,AI]
---

mAP全称mean average precision，是目标检测、信息检索等多个领域常见的一个性能评价指标。
这个指标在不同场合下虽然含义差不多，但具体计算方式却有多种。本文比较它们的差异。

<!--more-->

在分析不同之前，先介绍相同的部分。mAP定义为多次操作的平均精准率之均值。
换句话说，先对每次操作求一个平均精准率（average precision，AP），然后对所有的AP求均值（mean）。
后面求均值（mean）的部分都是一样的，区别只在于前面求AP的部分，主要体现在具体怎么个average法。
这里的每次操作，可以是目标检测中的一次检测，或是信息检索中的一次检索。

信息检索中，对于一次查询，系统会返回一系列查询结果，一般按相关度降序排列（目标检测也是类似）。
在计算AP时，我们取这些结果中的前N个。对于j从1到N的每一个值，根据前j个结果都可以算出一个精准率（precision）和召回率（recall）。
假设这j个结果中正确的数量为pos_num，则精准率precision_j = pos_num / j；假设所有正确结果的数量为ALL_POS_NUM，则召回率recall_j = pos_num / ALL_POS_NUM。

所谓的AP，就是对这些精准率进行平均。通常不是对所有精准率进行平均，而是选择其中一部分。
一种常用方法是选择那些召回率变化的j值。如果第j个结果是正确的，那么召回率相应增加（增加了1/ALL_POS_NUM）；如果第j个结果是错误的，那么召回率不变。所以这种计算方法只需要选择那些正确的结果对应的j值即可。
下面是此种计算方法的python代码实现。

```python
import random
random.seed(10)

N = 100
results = random.choices(["positive", "negative"], weights=[0.6, 0.4], k=N)
pos_num = 0
precisions = []
for i in range(N):
    if results[i] == "negative":
        continue
    if results[i] == "positive":
        pos_num += 1
        j = i + 1
        precision_j = pos_num / j
        precisions.append(precision_j)
ap = sum(precisions) / len(precisions)
print(ap)
```

另一种计算AP的思路是将其看作精准率和召回率曲线下的面积。这样计算的话需要把每个j处的召回率也计算出来。
参见如下代码：

```python
import random
random.seed(10)

N = 100
results = random.choices(["positive", "negative"], weights=[0.6, 0.4], k=N)
pos_num = 0
ALL_POS_NUM = results.count("positive")
precisions = []
recalls = []
for i in range(N):
    if results[i] == "negative":
        pass
    if results[i] == "positive":
        pos_num += 1
    j = i + 1
    precision_j = pos_num / j
    recall_j = pos_num / ALL_POS_NUM
    recalls.append(recall_j)
    precisions.append(precision_j)

delta_areas = []
for i in range(N):
    last_recall = recalls[i-1] if i > 0 else 0
    delta_areas.append(precisions[i] * (recalls[i] - last_recall))
ap = sum(delta_areas)
print(ap)
```

这种方法相当于对N个精准率加权求和，权重是前后两处的召回率差值（recalls[i] - last_recall）。
由于在结果错误的j值处召回率不变，这个权重差值将为0；而在结果正确的j值处，召回数量增加1，这个权重差值将为1/ALL_POS_NUM。
所以该加权求和等效于把结果正确的j值处的precison乘以1/ALL_POS_NUM然后累加起来。
这跟第一种方法对ALL_POS_NUM个precisions求平均计算出来的结果是一样的。
事实上，运行上述两段代码，输出值也确实相同。

其他不同的计算方法结果可能和上面不一致。差异除了N值的选取，主要体现在选取哪些j值点来进行计算。
还有一种潜在差异是在计算召回率时的ALL_POS_NUM如何定义。
本文将其定义为系统返回的N个结果中正确的数量，而其他有些计算方法会将其定义为待查询底库或待检测图片中真实的正确结果数量。
如果是后者，计算出的AP就会偏小一些。
