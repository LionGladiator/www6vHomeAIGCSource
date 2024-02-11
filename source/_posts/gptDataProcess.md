---
title: 数据处理
date: 2023-02-05 18:12:12
tags:
  - dataProcess
categories: 
  - AIGC
  - dataProcess   
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

# 数据处理 [1]
{% asset_img  'data_process.png' %}

### 质量过滤
+ 基于分类器的方法
+ 基于启发 式的方法

### 冗余去除
可以在**句子级**、**文档级**和**数据集级**等不同粒度上去重
在实践中应该 共同使用这三个级别的去重

### 隐私消除

### 词元切分
+ BPE
+ WordPiece
+ Unigram 词元分析

# 参考
1. 《大规模语言模型》 

1xx. [大模型时代下数据的重要性](https://zhuanlan.zhihu.com/p/639207933) 综述

1xx. [数据为王: Textbooks Are All You Need ](https://finisky.github.io/textbooks-are-all-you-need-summary/)

1xx. [数据为王：大模型预训练中的数据处理及思考—The RefinedWeb Dataset for Falcon LLM论文解读](https://zhuanlan.zhihu.com/p/641013454)

1xx. [大模型微调究竟需要多少数据：从三个现有代表工作看几组结论及一点思考 ](https://mp.weixin.qq.com/s/c50HrOfKOqgqGPVRHf6EpA)
   <<LIMa：Less Is More for Alignment>>
###  指令数据
1xx. [如何从数据集中自动识别高质量的指令数据-IFD指标的使用](https://zhuanlan.zhihu.com/p/658128530)

1xx. [大模型微调技巧 | 高质量指令数据筛选方法-MoDS](https://zhuanlan.zhihu.com/p/671183709)

### 其他
1xx. [哈工大｜15种NLP数据增强方法总结与对比](https://zhuanlan.zhihu.com/p/420295576)

1xx. [大模型研发核心：数据工程、自动化评估及与知识图谱的结合](https://hub.baai.ac.cn/view/28740)
   [大模型研发核心：数据工程、自动化评估及与知识图谱的结合 ](https://mp.weixin.qq.com/s/SvDnQD886E3DBtw8k9asgg)



1xx. [再看大模型预训数据质量如何评估：困惑度、错误L2范数和记忆化三种度量方法的效果对比分析研究](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648403976&idx=1&sn=694db5e2b3085b1610e8d19daa93a474)

1xx. [再谈大模型的预训数据清洗与微调数据生成：RedPajama数据处理框架与entity-centric指令生成方法解读 ](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648402104&idx=1&sn=7d4924b2a5a840e4ff3de43299248b1d)
    llama数据的复现项目SlimPajama
    
1xx. [“超越LLama 65B”的Falcon40B语言模型为什么好：再看精细化的数据清洗的重要性 ](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648401484&idx=1&sn=c49b5ca5fc962ca757d3a082b74f037a)    