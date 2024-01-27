---
title: Agent 原理
date: 2022-11-02 10:55:27
tags:
  - AIGC
categories: 
  - AIGC
  - Agent  
---

<p></p>
<!-- more -->


## 目录
<!-- toc -->


# 架构图  
{% asset_img 'agent-overview.jpg' %}

### 组件   [1][2]
Agent = LLM + plan[规划能力] + memory[记忆能力] +Tools[工具使用能力] 

###  Planning [6]
+ Task Decomposition
  - CoT 
  - ToT

+ Self-Reflection
  + ReAct 
  + Reflexion 
  + Chain of Hindsight 

### Memory [6]
+ Types of Memory
  - **Sensory memory** as learning **embedding representations for raw inputs, including text, image or other modalities**;
  - **Short-term memory** as **in-context learning**. It is short and finite, as it is restricted by the finite context window length of Transformer.
  - **Long-term memory** as the external **vector store** that the agent can attend to at query time, accessible via fast retrieval.

### Tool Use [6]
+ 让 agent 选择合适的工具 [8]
   - 可以 retrieve 相关示例来做 **few-shot prompt**。
   - 也可以进一步 **fine tune 特定模型**，例如之前的 Toolformer。

+ Research
  + **TALM** (Tool Augmented Language Models; Parisi et al. 2022) [6]
  + **Toolformer** (Schick et al. 2023)   [6]
  + **Gorilla** [8]

+ Production  [6]
  - ChatGPT **Plugins** 
  - OpenAI API **function calling**

# 类型  [3]
+ ReACT 范式 [7]
  把**融合了Reasoning和Acting**的一种范式，推理过程是浅显易懂，仅仅**包含thought-action-observation步骤**，很容易判断推理的过程的正确性，使用ReAct做决策甚至超过了强化学习.
  
  - chain-of-thought推理-问题
   事实幻想（fact hallucination）和错误传递（error propagation）
  
+ Self-ask

  Self-ask是一种follow-up的使用范式，仅仅包含follow-up, immediate answer步骤，至于follow-up多少个step，完全由它自己决定，估计这就是Self-ask的名字的由来。

+ Plan-and-execute agents
  本质上是先计划再执行，即先把用户的问题分解成一个个的子任务，然后再执行各个子任务，最后合并输出得到结果 
  
# 分类 [1][2][3]
+ Action agents  
    - Function Call
    - ReACT
      Thought: xxx
      Action: xxx
      Observation: xxx
    
+ Simulation agents 
    生成式智能体， CAMEL，  Generative Agents
    
+ Automomous Agent
    **AutoGPT**， **BabyAGI**,  **AutoGen[Microsoft]**
    MetaGPT，
    
+ 跨模态Agents
    HuggingGPT

+  ChatDev， AutoGen

# Example
### HuggingGPT 

### BabyAGI  [AIGC]

Plan-and-execute agents 

The **planning** is almost always done **by an LLM**.

The **execution** is usually done by a **separate agent (equipped with tools)**.

### AutoGPT[3][5]

AutoGPT 的核心逻辑是一个 Prompt Loop，步骤如下

1. AutoGPT 会基于一定策略自动组装 Command Prompt，这些首次会包含用户输入的 Name, Role和Goals 
2. Command Prompt 的目标不是为了拿到最终结果，而是通过 GPT Chat API(Thinking 的过程)返回下一步的 Command (包含name和arguments, 如`browser_website(url = "www.baidu.com")` )
3. 这些 Command 都是可扩展的，每一种命令代表一种外部能力(比如爬虫、Google搜索，也包括GPT的能力)，通过这些 Command 调用返回的 Result 又会成为到 Command Prompt 的组成元素，
4. 回到第 1 步往复循环，直到拿到最终结果结果（状态为“compelete”）

# 问题和局限性 [4]
+ 记忆召回问题
  只是做简单的 embedding 相似性召回，很容易发现召回的结果不是很好
  
+ 错误累积问题

+ 探索效率问题
  中途引入人工的判断干预和反馈输入

+ 任务终止与结果验证
  模型 agent 的工作如何终止也是一个挑战
  
# 挑战 [8]
### 如何让 agent 选择合适的工具
+ Toolformer - fine tune
+ Gorilla - retrieval，fine tune

### 不必要的工具使用
“Human Input”也写成一种工具，让模型来主动发起对人类的提问
[Human as a tool](https://python.langchain.com/docs/integrations/tools/human_tools)

### Agent 返回的格式不稳定
这里常见的做法是让 LLM **按照 json 这类常见的 schema 来返回**，一般稳定性会高一些（相比“Action:”这种）。
此外自动修复重试也很实用，可以利用 LangChain 里的 **output parsers** 来帮助完成。

### 记住之前的操作，避免重复
AutoGPT - retrieval 结合近期操作记录

### 处理超长的 observation
需要用一些工具从中**提取有用信息**，或者**放到外部存储中再借助 retrieval 来使用**。

### 专注于目标
简单的做法是**在 prompt 结尾处再把目标加上**，引起 agent 的注意。
另外像 BabyAGI，HuggingGPT 这种把 **planning 和 execution 分开**的做法也是很有用。**拆分的比较细**的任务往往步骤比较短，也不容易丢失目标。

### 结果评估
+ **评估最终结果**是否正确
+ **过程的细化评估**
  - 选择的中间步骤是否正确。
  - 生成 action 的 input 是否正确。
  - 生成的步骤序列是否合理高效。

# 参考
1. 公开课
2. 公开课
3. [2023年新生代大模型Agents技术,ReAct,Self-Ask,Plan-and-execute,以及AutoGPT, HuggingGPT等应用](https://zhuanlan.zhihu.com/p/642357544) ***  论文+代码
4. [AutoGPT与LLM Agent解析](https://zhuanlan.zhihu.com/p/622947810) *** 
5. [AutoGPT](https://github.com/Significant-Gravitas/AutoGPT) git
   [带界面的 AutoGPT 产品](https://link.zhihu.com/?target=https%3A//godmode.space/)
6. [LLM Powered Autonomous Agents ](https://lilianweng.github.io/posts/2023-06-23-agent/) paper 
7. [ReAct: Synergizing Reasoning and Acting in Language Models](https://react-lm.github.io/) paper
8. [LLM 全栈开发指南补遗](https://zhuanlan.zhihu.com/p/633033220)  Agents  ***
   [Harrison Chase: Agents](https://fullstackdeeplearning.com/llm-bootcamp/spring-2023/chase-agents/)  ***

