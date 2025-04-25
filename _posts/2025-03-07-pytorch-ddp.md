---
title: PyTorch的分布式数据并行
date: 2025-03-07 12:00:00
category: 技术
tags: [PyTorch,AI]
---

分布式数据并行（Distributed Data Parallel）是PyTorch中的一个重要模块。

<!--more-->

所谓数据并行，就是将数据分为不同的部分，在不同计算节点（GPU卡，可以是一机多卡或是多机多卡）上并行处理各部分，然后将它们的处理结果进行汇总。

与数据并行相对的是模型并行（Model Parallel），也就是将模型网络切割为多个部分放在不同节点上，一般是为了解决模型参数规模太大的问题。

数据并行既可以用作训练，也可以用作推理或测试，不过大多数时候是为了加速训练。
下面主要介绍下采用PyTorch的DDP进行训练的方法和我实际踩过坑的一些经验。

PyTorch官网的[一篇教程](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html)展示了多种启动DDP训练的方式，其中甚至包含了和模型并行相结合的例子。不过我觉得最常用也最方便的是该文档[最后一节](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#initialize-ddp-with-torch-distributed-run-torchrun)所介绍的，采用`torchrun`来发起训练：

    torchrun --nnodes=2 --nproc_per_node=8 --rdzv_id=100 --rdzv_backend=c10d --rdzv_endpoint=$MASTER_ADDR:29400 elastic_ddp.py

这里给出的命令是针对两个节点（两台服务器）、每节点8进程（每台服务器8张GPU卡）的场景，而且采用了`--rdzv_endpoint`这种新的方式来指定主机地址。这样在不同机器上运行同样命令即可。如果采用旧的方式，则需要在主从两台机器上运行不同的命令，如下所示（注意`--node_rank`的差异）：

    # 第一台机器上
    torchrun --nnodes=2 --nproc_per_node=8 --node_rank=0 --master_addr=$MASTER_ADDR elastic_ddp.py
    # 第二台机器上
    torchrun --nnodes=2 --nproc_per_node=8 --node_rank=1 --master_addr=$MASTER_ADDR elastic_ddp.py

`elastic_ddp.py`是支持了DDP的训练脚本，教程中也给出了示例。实际应用中，除了模型需要用`torch.nn.parallel.DistributedDataParallel`来封装一下，数据加载也需要适配。
具体来说，就是要给`torch.utils.data.DataLoader`传入一个`torch.utils.data.DistributedSampler`，使得每个进程采样加载互不重复的部分数据。另外，这个训练脚本最好写得灵活一些，在单卡上或不通过torchrun也能直接运行（`python elastic_ddp.py`）。

我在单机上调好DDP后，要扩展到多机时，一开始总是无法成功运行。排查了好久才发现，原来是两个机器环境不完全一样导致的！
虽然我没找到有关版本兼容性的文档，但实验下来发现：不仅PyTorch版本需要一致，Python的版本也要完全一致。
保险起见，最好操作系统也用同一版本。于是我后来干脆也采用docker来部署训练了。

另一个遇到的问题是有台服务器配置了多个网络接口，而DDP没能自动选到合适的，无法建立通信。
这个倒是按照[文档里的说明](https://pytorch.org/docs/stable/distributed.html#choosing-the-network-interface-to-use)，通过NCCL_SOCKET_IFNAME这一环境变量来手动指定一个网络就好了。

希望这些经验能对其他使用DDP的人有所帮助（尤其是多机运行时要确保环境完全相同这一点）。

