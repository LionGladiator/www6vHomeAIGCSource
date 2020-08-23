---
title: Rocketmq和Kafka中的事务
date: 2020-08-12 10:47:29
tags:
  - 消息系统
  - RocketMQ
  - Kafka
categories:
  - 分布式 
  - 中间件
  - 消息系统
  - 事务   
---

<p></p>
<!-- more -->

## 事务

### 1. Rocketmq事务
**半消息**保存在特殊的内部主题RMQ_SYS_TRANS_HALF_TOPIC中，使用的队列号固定为0。这个主题和队列对消费者是不可见的。

### 2. Kafka事务
组件
+ coordinate 协调者
+ 事务日志

语义
+ Kafka 的事务机制，更多的情况下被用来配合Kafka的幂等机制来实现 Kafka 的 **Exactly Once** 语义。
+ Kafka 的 Exactly Once 机制，是为了解决在**“读数据 - 计算 - 保存结果”（流计算）**这样的计算过程中数据不重不丢，而不是我们通常理解的使用消息队列进行消息生产消费过程中的 Exactly Once。

{% asset_img  kafka-tranaction.PNG  kafka事务-Data Flow %}

### 3. 事务总结
RocketMQ 和 Kafka 的事务都是基于**两阶段提交**来实现的事务，都利用了**特殊的主题中的队列和分区**来记录**事务日志**.
RocketMQ 和 Kafka 的事务，它们的适用场景是不一样的，**RocketMQ的事务适用于解决本地事务和发消息的数据一致性问题**，而 **Kafka 的事务则是用于实现它的 Exactly Once 机制，应用于实时计算的场景中**。



## 参考:

1. [RocketMQ 4.3正式发布，支持分布式事务](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2651008627&idx=1&sn=a308010e080e1aa7784abb4a1bcaadb7&chksm=bdbed6208ac95f3614f4055821e870882ea207e8a58af48f043e78cb4391e6f1206b41f86a88&scene=27#wechat_redirect)
2. [消息队列高手课 - 25 | RocketMQ与Kafka中如何实现事务？]() 李玥
3. [Kafka Exactly-Once 之事务性实现](http://matt33.com/2018/11/04/kafka-transaction/)  Matt's Blog-柳年思水