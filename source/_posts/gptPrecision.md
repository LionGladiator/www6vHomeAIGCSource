---
title: 混合精度 
date: 2024-02-01 22:29:42
tags:
  - Precision
categories:
  - AIGC
  - Precision
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

# 使用的混合精度原因
{%  asset_img  'solution.png'  %}

# 混合精度解决方案
### FP32 权重备份 [1][2]

这种方法主要是用于**解决舍入误差**的问题。

{% asset_img 'weight-backup.jpg' %} 

### Loss Scale[1][2]

Loss Scale 主要是为了**解决 fp16 underflow**的问题。

{% asset_img 'loss-scale.jpg' %} 


# 实战
###  llama半精度训练[20]
+ 现象
  loss先变大，再为0
  loss爆炸，loss消失
+ 解决方案
  padding=left
  改为padding=right

# 参考
### 原理
1. [混合精度](https://www.bilibili.com/video/BV1R94y1g78L?p=6)  *** V
2. [浅谈混合精度训练](https://zhuanlan.zhihu.com/p/103685761)
1xx. [全网最全-混合精度训练原理](https://zhuanlan.zhihu.com/p/441591808)  *** 
1xx. [【深度学习】混合精度训练与显存分析](https://zhuanlan.zhihu.com/p/608634079)

### 代码
20. [半精度训练与LLaMA2训练实战](https://www.bilibili.com/video/BV1CB4y1R78v/) 有代码
1xx. [低精度训练与大模型下载](https://www.bilibili.com/video/BV1y34y1M7t1/) 有代码
1xx. [PyTorch的自动混合精度（AMP）](https://zhuanlan.zhihu.com/p/165152789) 
1xx. [混合精度](https://tensorflow.google.cn/guide/mixed_precision?hl=zh-cn)