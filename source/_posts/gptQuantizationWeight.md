---
title: (原理)Weight Only(LLM.int8(), GPTQ, AWQ)
date: 2023-03-26 11:42:49
tags:
  - 量化
categories: 
  - AIGC
  - 量化  
---

<p></p>
<!-- more -->

# LLM.int8() [2]
### 原理
LLM.int8()是一种采用**混合精度分解**的量化方法。该方案先做了一个矩阵分解，对绝大部分权重和激活用8bit量化（vector-wise）。对**离群特征**的几个维度保留16bit，对其做高精度的矩阵乘法。

### 算法
LLM.int8() 通过三个步骤完成矩阵乘法计算:

+ 从输入的隐含状态中，按列提取**异常值 (离群特征，即大于某个阈值的值)**。
+ 对离群特征进行 FP16 矩阵运算，对非离群特征进行量化，做 INT8 矩阵运算；
+ 反量化非离群值的矩阵乘结果，并与离群值矩阵乘结果相加，获得最终的 FP16 结果。


# GPTQ[2]

### 原理
GPTQ 采用** int4/fp16 (W4A16) 的混合量化**方案，其中**模型权重**被量化为 **int4** 数值类型，而**激活值**则保留在 ** float16**，是一种**仅权重量化**方法。在推理阶段，模型权重被动态地反量化回 float16 并在该数值类型下进行实际的运算；同 OBQ  一样，GPTQ还是从**单层量化**的角度考虑，**希望找到一个量化过的权重，使的新的权重和老的权重之间输出的结果差别最小**。


GPTQ 将权重分组（如：128列为一组）为多个子矩阵（block）。对某个 block 内的所有参数逐个量化，每个参数量化后，需要适当调整这个 block 内其他未量化的参数，以弥补量化造成的精度损失。因此，GPTQ 量化需要准备校准数据集。

### 实现
AutoGPTQ 代码库集成到了 Transformers 中，让用户使用 GPTQ 算法在 **8 bit、4 bit、3 bit**，甚至是 **2 bit** 精度下量化和运行模型成为可能。当使用 **int4 量化**时，精度的下降可以忽略不计，同时在小批量推理上保持着与 fp16 基线相当的速度。需要注意的是，GPTQ 方法与 bitsandbytes 提出的训练后量化方法有所不同，GPTQ 需要**在量化阶段**提供一个**校准数据集**。

# AWQ [3]
### 技术原理
AWQ是一种对大模型**仅权重量化**方法。通过**保护更“重要”的权重不进行量化**，从而在不进行训练的情况下提高准确率。

### 实现
目前，除了官方提供了对于AWQ的支持（llm-awq）之外，社区有相当多的工具（如：**AutoAWQ**、**vLLM**、 HuggingFace TGI、LMDeploy、 **TensorRT-LLM**、FastChat 等）提供了对AWQ的支持。

# 总结
+ LLM.int8() 
 属于 round-to-nearest (RTN) 量化：舍入到最近的定点数。
 【keyword: 混合精度分解    离群值】
 
+ GPT-Q
 把量化问题视作**优化问题**，逐层寻找最优的量化权重。 
  【keyword: 混合量化  优化问题】
 

# 参考
1xx. [NLP大模型压缩关键技术解读：用于大型Transformer的8-bit矩阵乘法原理及其简单实现](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648399136&idx=1&sn=bd0a7237940c2ac800e06ae6d247349e)
   [大规模 Transformer 模型 8 比特矩阵乘简介 - 基于 Hugging Face Transformers、Accelerate 以及 bitsandbytes ](https://huggingface.co/blog/zh/hf-bitsandbytes-integration)  LLM.int8()
   
2. [大模型量化技术原理-LLM.int8()、GPTQ](https://zhuanlan.zhihu.com/p/680212402)

3. [大模型量化技术原理-AWQ、AutoAWQ](https://zhuanlan.zhihu.com/p/681578090)


1xx. 《8-实战Transformers模型量化》 Ai大模型微调
