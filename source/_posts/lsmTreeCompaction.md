---
title: LSM-Tree Compaction压缩
date: 2022-01-08 12:04:16
tags:
  - LSM-Tree
categories: 
  - 数据库
  - 基础  
---

<p></p>
<!-- more -->

## 放大 
+ Read amplification 读放大：完成每次读请求所需要的额外读盘次数。
+ Write amplification 写放大：完成每次写请求所需要的额外写盘数据。
+ Space amplification 空间放大：存放一定数据所需要的额外存盘空间。

## 评估和设计 compaction 策略的关键性能指标[1]
+ 压缩触发器
+ 数据布局(Data Layout)
   + leveling
   + tiering
+  压缩粒度
+  数据移动策略

{% asset_img compaction-summary.png  LSM-Tree Compaction Summary %}

##  10 种 compaction 策略[todo]

## KV分离[3]
+ Wisckey 

## 参考
1. [Research_R42.5 Constructing and Analyzing the LSM Compaction Design Space](https://www.bilibili.com/video/BV12U4y177g3?vd_source=f6e8c1128f9f264c5ab8d9411a644036)  论文 good VLDB2021 bilibili
2. [VLDB 2021论文概述](https://zhuanlan.zhihu.com/p/413463723) 
3. [LSM-Tree:从入门到放弃——放弃：LSM Tree的Compaction机制探讨和分析](https://www.zhihu.com/search?hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2094178256%7D&hybrid_search_source=Entity&q=compaction&search_source=Entity&type=content) 