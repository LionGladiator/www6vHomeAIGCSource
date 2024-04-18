---
title:  (原理)RAG OpenAI案例
date: 2022-12-27 11:11:05
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

# OpenAI RAG 案例[3]
{% asset_img 'openai-rag.jpg' %}

1. retrieval with consine similarity
2. **HyDE retrieval** [5]
   Fine-tune Embeddings
   **Chunk/embedding experiments**
3. **Reranking** [6][8]
   Classification step
4. Prompt engineering
   **Tool use**
   **Query expansion**[5]

### Query Transformations[5]
+ **Query expansion**
  Multi-query retriever 
+ **HyDE**
+ Step back prompting
 [抽象prompting]
+ Rewrite-Retrieve-Read

### Query Construction [4]
{% asset_img 'structured_data_stacks.jpg' %}

| **Examples**                | **Data source** | **References**                                               |
| --------------------------- | --------------- | ------------------------------------------------------------ |
| **Text-to-metadata-filter** | Vectorstores    | [**Docs**](https://python.langchain.com/docs/modules/data_connection/retrievers/self_query/?ref=blog.langchain.dev#constructing-from-scratch-with-lcel) |
| **Text-to-SQL**             | SQL DB          | [**Docs**](https://python.langchain.com/docs/use_cases/qa_structured/sql?ref=blog.langchain.dev)**,** [**blog**](https://blog.langchain.dev/llms-and-sql/)**,** [**blog**](https://blog.langchain.dev/incorporating-domain-specific-knowledge-in-sql-llm-solutions/) |



+ Text-to-metadata-filter [7]

A **self-querying** retriever is one that, as the name suggests, has the  ability to query itself. Specifically, given any natural language query, the retriever uses a query-constructing LLM chain to write a **structured query** and then applies that structured query to its underlying  VectorStore. This allows the retriever to not only use the user-input  query for semantic similarity comparison with the contents of stored  documents but to also **extract filters from the user query on the  metadata of stored documents and to execute those filters**.


# Advanced RAG
### 架构 [1]
  - 离线 index
  - 在线 查询

{% asset_img 'rag.jpg' %}


# 参考
1. [Deconstructing RAG](https://blog.langchain.dev/deconstructing-rag/) ***

3. [Applying OpenAI's RAG Strategies](https://blog.langchain.dev/applying-openai-rag/)   *** 

4. [Query Construction](https://blog.langchain.dev/query-construction/) ***

5. [Query Transformations](https://blog.langchain.dev/query-transformations/)

6. [Say Goodbye to Irrelevant Search Results: Cohere Rerank Is Here](https://txt.cohere.com/rerank/)
   [Rerank](https://github.com/langchain-ai/langchain/tree/master/templates/rag-pinecone-rerank)
   [Cohere Reranker](https://python.langchain.com/docs/integrations/retrievers/cohere-reranker)
   
7. [self_query](https://github.com/langchain-ai/langchain/blob/master/docs/docs/modules/data_connection/retrievers/self_query.ipynb)

8. [RAG Fusion](https://github.com/langchain-ai/langchain/blob/master/cookbook/rag_fusion.ipynb)
   [Forget RAG, the Future is RAG-Fusion](https://towardsdatascience.com/forget-rag-the-future-is-rag-fusion-1147298d8ad1)
   
### query 改写
1xx. [知识图谱用于细粒度大模型幻觉评估：兼论Langchain-RAG问答中的问题改写范式 ](https://mp.weixin.qq.com/s?__biz=MzAxMjc3MjkyMg==&mid=2648406156&idx=1&sn=d91a4df105c4fc4c9523f7141bc1c24d)
  RAG:  rewrite , Step back prompting, fusion 
1xx. [业界总结｜搜索中的Query理解](https://zhuanlan.zhihu.com/p/393914267) ***
1xx. [智能扩充机器人的“标准问”库之Query生成](https://zhuanlan.zhihu.com/p/149429784)