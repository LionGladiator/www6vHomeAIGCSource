---
title: PEFT Lora
date: 2023-01-05 12:04:14
tags:
  - PEFT
categories:
  - AIGC  
  - PEFT
---

<p></p>
<!-- more -->


## 基于bloom的微调
+ 简单基础  [2]
  - 基座模型  
    Langboat/bloom-1b4-zh 
  - 数据集
    shibing624/alpaca-zh
  
+ 稍复杂[1]
  - 基座模型 
    bloomz-560m 
  - 数据集
    ought/raft

# Lora 参数 
+ LoraConfig [2]
``` python
LoraConfig( 
base_model_name_or_path='Langboat/bloom-1b4-zh', 
task_type=<TaskType.CAUSAL_LM: 'CAUSAL_LM'>, 
inference_mode=False, 
r=8, 
target_modules={'query_key_value'}, 
lora_alpha=32, 
lora_dropout=0.1, 
modules_to_save=None)
```

+ 参数说明 [1]
    - task_type：指定任务类型。如：条件生成任务（SEQ_2_SEQ_LM），因果语言建模（CAUSAL_LM）等。
    - inference_mode：是否在推理模式下使用Peft模型。
    - r： LoRA低秩矩阵的维数。关于秩的选择，通常，使用4，8，16即可。
    - lora_alpha： LoRA低秩矩阵的缩放系数，为一个常数超参，调整alpha与调整学习率类似。
    - lora_dropout：LoRA 层的丢弃（dropout）率，取值范围为[0, 1)。
    - target_modules：要替换为 LoRA 的模块名称列表或模块名称的正则表达式。针对不同类型的模型，模块名称不一样.

+ target_modules [1]
在 PEFT 中支持的模型默认的模块名如下所示：
``` python
TRANSFORMERS_MODELS_TO_LORA_TARGET_MODULES_MAPPING = {
    "t5": ["q", "v"],
    "mt5": ["q", "v"],
    "bart": ["q_proj", "v_proj"],
    "gpt2": ["c_attn"], #
    "bloom": ["query_key_value"], #
    "blip-2": ["q", "v", "q_proj", "v_proj"],
    "opt": ["q_proj", "v_proj"],
    "gptj": ["q_proj", "v_proj"],
    "gpt_neox": ["query_key_value"],
    "gpt_neo": ["q_proj", "v_proj"],
    "bert": ["query", "value"], #
    "roberta": ["query", "value"],
    "xlm-roberta": ["query", "value"],
    "electra": ["query", "value"],
    "deberta-v2": ["query_proj", "value_proj"],
    "deberta": ["in_proj"],
    "layoutlm": ["query", "value"],
    "llama": ["q_proj", "v_proj"],  #
    "chatglm": ["query_key_value"],  #
    "gpt_bigcode": ["c_attn"],
    "mpt": ["Wqkv"],
}
```

# 参考
1. [大模型参数高效微调技术实战（五）-LoRA](https://zhuanlan.zhihu.com/p/649315197)
   [bloom Lora](https://github.com/www6v/llm-action/blob/main/train/peft/clm/peft_lora_clm.ipynb) git
   
2. [【手把手带你实战HuggingFace Transformers-高效微调篇】LoRA 原理与实战](https://www.bilibili.com/video/BV13w411y7fq/) V
    [bloom Lora-origin](https://github.com/www6v/transformers-code/blob/master/03-PEFT/21-lora/chatbot_lora.ipynb)  [bloom Lora-origin](https://colab.research.google.com/github/www6v/transformers-code/blob/master/03-PEFT/21-lora/chatbot_lora.ipynb) git   origin运行有问题
    [bloom Lora-modify](https://github.com/www6v/transformers-code/blob/master/03-PEFT/21-lora/chatbot_lora%5Bworkable%5D.ipynb)  [bloom Lora-modify](https://colab.research.google.com/drive/1SNy35_CJOobe4AxAecMZJo4LX1TjXvTm) 修改过可以在colab运行的代码

100. [Finetuning LLMs with LoRA and QLoRA: Insights from Hundreds of Experiments](https://lightning.ai/pages/community/lora-insights/) ***
     [几百次大模型LoRA和QLoRA 微调实践的经验分享](https://www.bilibili.com/video/BV16u4y1a7MH/) V
