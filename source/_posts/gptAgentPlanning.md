---
title: Agent Planning
date: 2023-05-13 06:57:12
tags:
  - AIGC
categories: 
  - AIGC
  - Agent  
---

<p></p>
<!-- more -->



# Patterns  [3]
+ ReACT 范式
  把**融合了Reasoning和Acting**的一种范式，推理过程是浅显易懂，仅仅**包含thought-action-observation步骤**，很容易判断推理的过程的正确性，使用ReAct做决策甚至超过了强化学习.  
  - chain-of-thought推理-问题
   事实幻想（fact hallucination）和错误传递（error propagation）
  
+ Self-ask
  Self-ask是一种follow-up的使用范式，仅仅包含follow-up, immediate answer步骤，至于follow-up多少个step，完全由它自己决定，估计这就是Self-ask的名字的由来。

+ Plan-and-execute agents
  本质上是先计划再执行，即先把用户的问题分解成一个个的子任务，然后再执行各个子任务，最后合并输出得到结果 


# 参考
3. [2023年新生代大模型Agents技术,ReAct,Self-Ask,Plan-and-execute,以及AutoGPT, HuggingGPT等应用](https://zhuanlan.zhihu.com/p/642357544) ***  论文+代码

1xx. [大语言模型智能体规划能力综述: 分类、任务分解、选择、反思、记忆增强 ](https://mp.weixin.qq.com/s/1POXDVJDv3ob1HqpKjb3Mg) 翻译

### Planning
20. {% post_link 'gptAgentReflection' %} self

2xx. [引领语言智能：从思维链推理到语言智能体的探索指南 [译]](https://baoyu.io/translations/ai-paper/2311.11797-igniting-language-intelligence-the-hitchhikers-guide-from-chain-of-thought-reasoning-to-language-agents) paper
2xx. [ReWOO: 高效增强语言模型中解偶观测和推理](https://zhuanlan.zhihu.com/p/671491031)
