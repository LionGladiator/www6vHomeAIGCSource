---
title: Serverless
date: 2019-10-10 10:50:53
tags:
  - serverless
categories:
  - 分布式 
  - 云原生
  - serverless
---

<p></p>
<!-- more -->

## 一. Serverless定义

Serverless = Faas + Baas[5]

> Faas: 相对通用的形式表示无服务计算
Faas例子: Cloud Functions

> Baas:特定领域的、高度优化的无服务计算实现
Baas例子: S3(Object Storage), DynamoDB（Key-Value Database），Cloud Pub/Sub (Messaging) 

<div style="text-align: center; width: 70%; height: 70%"> 
![serverless](https://user-images.githubusercontent.com/5608425/66545940-5fefed00-eb6e-11e9-85d8-3279f6db2d61.jpg)
Serverless
</div>



## 二. serverless和serverful的对比
<div style="text-align: center;">
![serverless-serverful](https://user-images.githubusercontent.com/5608425/66544428-af341e80-eb6a-11e9-9a82-527cb0268300.jpg)
serverless和serverful的对比
</div>

+ serverless
1. 事件驱动的
2. **计算与存储解耦**
   状态存储在外部（Baas） 
3. 资源受限的
4. **以实际使用的资源量付费，而不是根据分配的资源数**
5. **执行代码而不需要管理资源分配**
   自动提供如下服务:
   选择实例、扩缩容、部署、容错、监控、日志、安全补丁
6. **自动扩缩容**
   有负载时自动**扩容**;
   没有负载的情况下将资源一直**缩容**到零;
7. 多租户，安全隔离

## 三. 使用的场景

## 四. 挑战和优化

### 4.1 加快函数运行环境的创建
1. AWS Lambda 中使用的方法是维护一个热的 VM 实例池[5]
2. 阿里 应用容器启动加速[3]
<div style="text-align: center; width: 80%; height: 80%"> 
   ![ali-serverless-optimiaze](https://user-images.githubusercontent.com/5608425/66549078-18b92a80-eb75-11e9-9c23-f86a1e711934.jpg)
</div>
+ L1: 容器提前启动，并且对容器进行冻结。
    应用实例 CPU 占用率为0，RAM 占用相当于之前的1/20。
+ L2: 磁盘快照： 容器持久化到磁盘
    时间消耗主要是数据的网络传输时间+内存拷贝时间。
+ L3: 完全冷启动

### 4.2 混合部署
更小的资源占用率， 更短的运行时间更易于混合部署。[3][5]


## 参考:

1. [让业务感知不到服务器的存在——基于弹性计算的无服务器化实践](https://cloud.tencent.com/developer/article/1158774)  腾讯 未
2. [对Serverless架构的一点体验和思考](https://yq.aliyun.com/articles/160370?spm=5176.8067842.tagmain.56.9Vk1fX) 阿里 未
3. [研发运维效率提升100%，机器成本下降50%，阿里巴巴在 Serverless 计算领域的探索](https://mp.weixin.qq.com/s/Gj_qPPTn6KN065qUu6e-mw)  阿里 
   I. 提高启动时间 II. 混合部署
4. [云计算中的Xaas](../../../../2019/02/07/xaas/) self
5. [无服务计算的未来和挑战: A Berkeley View on Serverless Computing](https://mp.weixin.qq.com/s/7qJUzf8xrGihPPLsvwPEig) good
