---
title: RAG 实践
date: 2022-12-31 07:42:52
tags:
  - RAG
categories: 
  - AIGC
  - RAG  
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

# 基于文本的RAG
### Langchain-Chatchat 架构
{% asset_img 'langchain+chatglm.jpg' %}

+ 组件
  + 本地知识库
  + Embedding 模型
  + 向量数据库
  + Prompt Template

### Langchain-Chatchat
+ 部署 
  - windows 10 [5] 
    部署本地， 没显存，卡
  - Linux [2]
    部署   32C125G ，没显存， 推理很慢 
  - Docker 


# 多模态RAG-多向量检索器 [10][11]
### semi-structured (tables + text) RAG [12] 
 分析pdf中表格 

### multi-modal (text + tables + images) RAG [13] 

###  private multi-modal (text + tables + images)  RAG [14]

分析PDF中图片
**Option 1** 
* Use multimodal embeddings **(such as [CLIP](https://openai.com/research/clip))** to embed images and text
* Retrieve both using similarity search
* Pass **raw images and text chunks** to a multimodal LLM for answer synthesis 

**Option 2** 
* Use a multimodal LLM (such as [GPT4-V](https://openai.com/research/gpt-4v-system-card), [LLaVA](https://llava.hliu.cc/), or [FUYU-8b](https://www.adept.ai/blog/fuyu-8b)) to produce **text summaries from images**
* Embed and retrieve text 
* Pass text chunks to an LLM for answer synthesis 

**Option 3** 
* Use a multimodal LLM (such as [GPT4-V](https://openai.com/research/gpt-4v-system-card), [LLaVA](https://llava.hliu.cc/), or [FUYU-8b](https://www.adept.ai/blog/fuyu-8b)) to produce text summaries from images
* Embed and retrieve image summaries with a reference to the raw image 
* Pass **raw images and text chunks** to a multimodal LLM for answer synthesis  

### 组件
+ pdf解析
  unstructured
+ store
  MultiVectorRetriever - 元数据+数据

# Vector+KG RAG[15][16]

# Data processing[17]
长文本   变成   QA pair
+ 规则匹配
+ 利用LLM抽取
+ 人工处理

# 参考
### 文本
1. [Langchain-Chatchat ](https://github.com/chatchat-space/Langchain-Chatchat) master
   Langchain 与 ChatGLM 等语言模型的本地知识库问答
   [Langchain-Chatchat](https://github.com/chatchat-space/Langchain-Chatchat/tree/v0.2.4)  v0.2.4 
   [langchain-ChatGLM](https://gitee.com/deepeye/langchain-ChatGLM)  gitee 
   
2. [Colab for Langchain-Chatchat](https://github.com/www6v/Langchain-Chatchat-Colab)   linux 可以部署  v0.2.6
3. [langChain-ChatGLM 尝试，踩坑记录](https://zhuanlan.zhihu.com/p/649055955)
4. [Langchain-Chatchat + 阿里通义千问Qwen 保姆级教程 | 次世代知识管理解决方案](https://zhuanlan.zhihu.com/p/651189680)    Langchain-Chatchat + 通义千问
5. [win10 安装 Langchain-Chatchat 避坑指南（2023年9月18日v0.2.4版本，包含全部下载内容！）](https://blog.csdn.net/weixin_43094965/article/details/133044128)  

### 多模态
10. [检索增强生成（RAG）有什么好的优化方案？](https://www.zhihu.com/question/628651389/answer/3321989558) 

11. [Multi-Vector Retriever for RAG on tables, text, and images](https://blog.langchain.dev/semi-structured-multi-modal-rag/) *** 

12. [Semi_Structured_RAG](https://github.com/langchain-ai/langchain/blob/master/cookbook/Semi_Structured_RAG.ipynb)
    [Advanced-RAG semi_structured_data](https://github.com/www6v/AIGC/blob/master/Advanced-RAG/01_semi_structured_data.ipynb)   code 半结构化-解析pdf中的表格，  运行没问题，能问表格中的数据
    
13. [Semi_structured_and_multi_modal_RAG](https://github.com/langchain-ai/langchain/blob/master/cookbook/Semi_structured_and_multi_modal_RAG.ipynb)  

14. [Private Semi-structured and Multi-modal RAG w/ LLaMA2 and LLaVA](https://github.com/www6v/AIGC/blob/master/langchain-cookbook/Semi_structured_multi_modal_RAG_LLaMA2.ipynb)  code 多模态- 解析pdf中的图片  运行有问题 
    [Private Semi-structured and Multi-modal RAG w/ LLaMA2 and LLaVA](https://github.com/langchain-ai/langchain/blob/master/cookbook/Semi_structured_multi_modal_RAG_LLaMA2.ipynb)
  
15. [Enhanced QA Integrating Unstructured Knowledge Graph Using Neo4j and LangChain](https://neo4j.com/developer-blog/unstructured-knowledge-graph-neo4j-langchain/)  

16. [Using a Knowledge Graph to implement a DevOps RAG application](https://blog.langchain.dev/using-a-knowledge-graph-to-implement-a-devops-rag-application/)

17. <<大模型结合 RAG 构建客服场景自动问答系统>>  NVIDIA大模型日系列活动  

100. [LlamaHub](https://llamahub.ai/) 未  
      Mix and match our Data Loaders and Agent Tools to build custom RAG apps or use our LlamaPacks as a starting point for your retrieval use cases.

