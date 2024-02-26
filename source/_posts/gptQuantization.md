---
title: 量化
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


# PTQ
### LLM.int8() [6]
### GPTQ [6]
### AWQ [7]

# 实战-PTQ[1]
###  量化与8bit模型训练
``` python
import torch
model = AutoModelForCausalLM.from_pretrained("D:/Pretrained_models/modelscope/Llama-2-7b-ms", low_cpu_mem_usage=True, 
                                             torch_dtype=torch.half, device_map="auto", load_in_8bit=True)
```

# 实战2-量化推理 量化推理 [8]
+ Training的模型
{% asset_img 'dirs.png' %}

+ 合并后的模型
{% asset_img 'dir.png' %}

+ 4bit量化推理
{% asset_img 'xtuner-chat.png' %}

> Training的时候要用tmux
``` shell
$ tmux new -s finetune
$ tmux attach -t finetune
$ ctcl +b , D
```

> 16bit量化推理慢,  要用4bit量化推理



# 低精度训练方法[chat]

+ 半精度浮点数（FP16）训练
+ 混合精度训练（Mixed Precision Training）
+ 量化训练（Quantization Training）

# 参考
1.  [ 量化与8bit模型训练](https://www.bilibili.com/video/BV1EN411g7Yn/) V
    [【手把手带你实战HuggingFace Transformers-低精度训练篇】量化与8bit模型训练](https://www.bilibili.com/video/BV1EN411g7Yn/)
   [llama2_lora_8bit.ipynb](https://github.com/www6v/transformers-code/blob/master/04-Kbit%20Training/26-8bits_training/llama2_lora_8bit.ipynb)
2. [大模型训练｜概念篇](https://zhuanlan.zhihu.com/p/649460612)
3. << An Introduction to Quantization of Large Language Model >> 
4. 4.1[pytorch Quantization](https://pytorch.org/docs/stable/quantization.html)
   4.2 [Introduction to Quantization on PyTorch](https://pytorch.org/blog/introduction-to-quantization-on-pytorch/) 
5. [大模型量化概述](https://zhuanlan.zhihu.com/p/662881352)
6. [大模型量化技术原理-LLM.int8()、GPTQ](https://zhuanlan.zhihu.com/p/680212402)
7. [大模型量化技术原理-AWQ、AutoAWQ](https://zhuanlan.zhihu.com/p/681578090)

8. [internLM fine-tuning on xtuner](https://github.com/www6v/tutorial/tree/main/xtuner)   
   [(4)XTuner 大模型单卡低成本微调实战](https://www.bilibili.com/video/BV1yK4y1B75J/) V
9. {% post_link 'gptPEFTQLora' %}   self

1xx. [神经网络低比特量化中训练和推理是如何实现的？](https://www.zhihu.com/question/510246227)

1xx. [NLP大模型压缩关键技术解读：用于大型Transformer的8-bit矩阵乘法原理及其简单实现](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648399136&idx=1&sn=bd0a7237940c2ac800e06ae6d247349e)
   [大规模 Transformer 模型 8 比特矩阵乘简介 - 基于 Hugging Face Transformers、Accelerate 以及 bitsandbytes ](https://huggingface.co/blog/zh/hf-bitsandbytes-integration)

