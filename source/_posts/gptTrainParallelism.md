---
title: (原理)分布式并行Training 
date: 2023-01-06 05:51:54
tags:
  - train
categories:
  - AIGC  
  - train
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->


# 分布式并行 [1]
{% asset_img 'pararllelTraining.jpg' %}

### 数据并行 [4]
数据并行可以分为**中心化方式**的和**无中心化方式**的，对应于pytorch里面的**DataParallel**和**DistributedDataParallel(DDP)**

### 模型并行 
**张量并行**与**流水线并行**都属于**模型并行**，
区别在于对模型参数的切分“方向”不同：
**张量并行**把模型的**每层进行切分 (intra-layer)**，而**流水线并行**则**按层进行切分 (inter-layer) 并在不同设备处理**。[2]


##### 张量并行 [3]
 {% asset_img 'tensor.png' %}

+ Megatron-LM（1D）
+ Colossal-AI（2D、2.5D、3D）

#####   流水线并行 [3]
{% asset_img 'pipeline.png' %}

+ GPipe
+ PipeDream

### 多维混合并行[5]
##### DP + PP
##### 3D 并行（DP + PP + TP）
##### ZeRO-DP + PP + TP


# 框架
+ Megatron-LM（张量并行）
+ DeepSpeed（Zero-DP）
+ Colossal-AI（高维模型并行，如2D、2.5D、3D）
+ Alpa（自动并行）


# 参考
### 
1. [How to Train Really Large Models on Many GPUs? ](https://lilianweng.github.io/posts/2021-09-25-train-large/)

2. [大模型分布式训练的并行策略](https://finisky.github.io/how-to-train-large-language-model/) *

3. [大模型并行训练指南：通俗理解Megatron-DeepSpeed之模型并行与数据并行](https://blog.csdn.net/v_JULY_v/article/details/132462452)  ***

4. [[Transformer 101系列] LLM分布式训练面面观](https://zhuanlan.zhihu.com/p/664604792) ***

5. [大模型分布式训练并行技术（六）-多维混合并行](https://zhuanlan.zhihu.com/p/661279318)

6. [上半年大模型遍地开花，大模型发展中有哪些经验和教训？](https://www.zhihu.com/question/601594836/answer/3032763174) 分布式训练框架


1xx. [大模型分布式训练并行技术（一）-概述](https://zhuanlan.zhihu.com/p/598714869)

1xx. [分布式训练硬核技术——通信原语](https://zhuanlan.zhihu.com/p/465967735) 

1xx. [图解大模型训练之：流水线并行（Pipeline Parallelism），以Gpipe为例](https://zhuanlan.zhihu.com/p/613196255)  系列文章 

1xx. [图解大模型训练之：张量模型并行(TP)，Megatron-LM](https://zhuanlan.zhihu.com/p/622212228) ***


1xx. [全网最全-超大模型+分布式训练架构和经典论文](https://zhuanlan.zhihu.com/p/450854172) 

1xx. [大规模训练系列之技术挑战](https://zhuanlan.zhihu.com/p/350707888)

### 实战
1xx. [大模型流水线并行（Pipeline）实战](https://zhuanlan.zhihu.com/p/636488690)

### 其他
1xx. [第1章：DeepSpeed-Chat 模型训练实战](https://techdiylife.github.io/big-model-training/deepspeed/deepspeed-chat.html)  Bili 
      [DeepSpeed-Chat](https://github.com/microsoft/DeepSpeedExamples/tree/master/applications/DeepSpeed-Chat)