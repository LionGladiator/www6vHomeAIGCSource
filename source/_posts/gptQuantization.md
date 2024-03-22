---
title: (原理)模型压缩-量化
date: 2023-02-19 17:00:25
tags:
  - 量化
categories: 
  - AIGC
  - 量化  
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

# 量化
### 量化的定义  [3]
{% asset_img 'quantization.jpeg' %}

### 量化的两个阶段  [3]
{% asset_img 'twoProcedures.jpeg' %}


### 量化分类
+ 量化分类 [3][5]
  + Quantization-Aware Training (QAT)  
    **Need more data and time, More accurate**
  + Quantization-Aware Fine-tuning(QAF)   [9]
  + Post-Training Quantization (PTQ)  
    **Need fewer data and time, Less accurate**

+  PyTorch 支持的三种量化类型 [4]
   - dynamic quantization (weights quantized with activations read/stored in floating point and quantized for compute)
   - static quantization (weights quantized, activations quantized, calibration required post training)    **->PTQ**
   - static quantization aware training (weights quantized, activations quantized, quantization numerics modeled during training)   **->QAT**

### Quantization
+ QAT -> Expensive
+ PTQ -> More feasible than QAT

# PTQ 分类
### Weight Quantization  [10]
- LLM.int8() [6]
- GPTQ [6]
- AWQ [7]

### Weight and Activation Quantization [10]
- SmoothQuant[8]

### 比较[1]

|                   | Weight only quant | smoothquant(PTQ ) | fp8(PTQ ) |
| ----------------- | ----------------- | ----------------- | --------- |
| Latency Reduction | ★★                | ★★★(best)         | ★★★(best) |
| Modal Acc         | ★★                | ★                 | ★★★(best) |
| Memory Saving     | ★★                | ★★★(best)         | ★★★(best) |
| Ease of use       | ★★★(best)         | ★★                | ★         |

# 低精度训练方法[chat]

+ 半精度浮点数（FP16）训练
+ 混合精度训练（Mixed Precision Training）
+ 量化训练（Quantization Training）

# 参考
1. [大语言模型推理：低精度最佳实践](https://www.bilibili.com/video/BV1h44y1c72B) V
2. [大模型训练｜概念篇](https://zhuanlan.zhihu.com/p/649460612)
3. << An Introduction to Quantization of Large Language Model >> 
4. 4.1[pytorch Quantization](https://pytorch.org/docs/stable/quantization.html)
   4.2 [Introduction to Quantization on PyTorch](https://pytorch.org/blog/introduction-to-quantization-on-pytorch/) 
5. [大模型量化概述](https://zhuanlan.zhihu.com/p/662881352)
6. [大模型量化技术原理-LLM.int8()、GPTQ](https://zhuanlan.zhihu.com/p/680212402)
7. [大模型量化技术原理-AWQ、AutoAWQ](https://zhuanlan.zhihu.com/p/681578090)

8. [大模型量化技术原理-SmoothQuant ](https://juejin.cn/post/7330079146515611687)
9. {% post_link 'gptPEFTQLora' %}   self
10. 《A Survey on Model Compression for Large Language Models》


1xx. [神经网络低比特量化中训练和推理是如何实现的？](https://www.zhihu.com/question/510246227)

1xx. [NLP大模型压缩关键技术解读：用于大型Transformer的8-bit矩阵乘法原理及其简单实现](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648399136&idx=1&sn=bd0a7237940c2ac800e06ae6d247349e)
   [大规模 Transformer 模型 8 比特矩阵乘简介 - 基于 Hugging Face Transformers、Accelerate 以及 bitsandbytes ](https://huggingface.co/blog/zh/hf-bitsandbytes-integration)

   

