---
title: Rocketmq中的事务
date: 2020-08-12 10:47:29
tags:
  - 消息系统
categories:
  - 消息系统
  - RocketMQ   
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

##  RocketMQ 事务

+ 事务消息【6，7】
	- ebay模式，half消息
		+ 1.提交流程（正向流程）
			1.发消息（half消息）
			2. 执行本地事务
			3. 更新事务消息的最终状态
		+ 2.回查流程（逆向流程）
			找到尚未提交的超时事务
		+ 大事务 = 小事务 + 异步（阿里中台战略）【8】

+ 半消息
  **半消息**保存在特殊的内部主题RMQ_SYS_TRANS_HALF_TOPIC中，使用的队列号固定为0。这个主题和队列对消费者是不可见的。
  
##  事务总结 [2]
RocketMQ 和 Kafka 的事务都是基于**两阶段提交**来实现的事务，都利用了**特殊的主题中的队列和分区**来记录**事务日志**.
RocketMQ 和 Kafka 的事务，它们的适用场景是不一样的，**RocketMQ的事务适用于解决本地事务和发消息的数据一致性问题**，而 **Kafka 的事务则是用于实现它的 Exactly Once 机制，应用于实时计算的场景中**。

## 参考
1. [RocketMQ 4.3正式发布，支持分布式事务](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2651008627&idx=1&sn=a308010e080e1aa7784abb4a1bcaadb7&chksm=bdbed6208ac95f3614f4055821e870882ea207e8a58af48f043e78cb4391e6f1206b41f86a88&scene=27#wechat_redirect)
2. 《消息队列高手课 - 25 | RocketMQ与Kafka中如何实现事务？》 李玥
##### 事务
6. [分布式开放消息系统(RocketMQ)的原理与实践](https://zhuanlan.zhihu.com/p/396726719)   CHEN川  ***  
   消息的顺序问题  消息的重复问题
7. [RocketMQ 4.3正式发布，支持分布式事务](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2651008627&idx=1&sn=a308010e080e1aa7784abb4a1bcaadb7)
8. {%  post_link 'middleStage' %}  self