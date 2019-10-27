---
title: 分布式一致性总结
date: 2016-02-09 18:19:56
tags:
  - 分布式
  - 一致性
  - 事务
categories: 
  - 分布式
  - 一致性    
  - 事务
---

<p></p>
<!-- more -->

[raft]:../../../../2019/06/21/raft
[gossip-visualization]:https://rrmoelker.github.io/gossip-visualization/
[AT Mode]:https://github.com/seata/seata/wiki/AT-Mode  
[MT Mode]:https://github.com/seata/seata/wiki/MT-Mode

{% asset_img  consistent.jpg  分布式一致性总结 %}


## 一. 一致性

<div style="text-align: center;">
![consistent-relationship](https://user-images.githubusercontent.com/5608425/65506192-989c8f00-defd-11e9-8ce4-df9b2bd8a96f.jpg)
</div>

### 1.1 强一致性
强一致性|协议|特性|举例
:-:|:-:|:-:|:-:
强一致性|2阶段,3阶段 #1| 延迟大，吞吐低。全局锁资源| JTA(XA)
强一致性|paxos #1|难理解，延迟大，吞吐中等，全局锁资源|分布式锁系统Chubby			
顺序一直性（Sequential Consistency）| 逻辑时钟 |类似多线程程序执行顺序的模型| Zookeeper的读 <br>1.两个主流程，三个阶段 <br> 2.Zab（Qurum）:2f+1个节点，允许f个节点失败

> Lamport提出**逻辑时钟**是为了解决分布式系统中的时序问题，即如何定义a在b之前发生.
  Java中有happen-before  

> **线性一致性**  #1： 严格一致性（Strict Consistency）或者原子一致性（Atomic Consistency） 一个操作对于系统的其他部分是不可中断的	

> **顺序一致性**  
  + 任何一次读写操作都是按照某种特定的顺序。
  + 所有进程看到的读写操作顺序都保持一致。

> **顺序一致性**虽然通过逻辑时钟保证所有进程保持一致的读写操作顺序，但这些读写操作的顺序跟实际上发生的顺序并不一定一致。而**线性一致性**是严格保证跟实际发生的顺序一致的。

### 1.2 弱一致性

弱一致性|协议|特性|举例
:-:|:-:|:-:|:-:
最终一致性(因果一致性)| 向量时钟 Vector clock[向量时钟] 图1 || 微信朋友圈的评论, Dynamo
最终一致性（反熵Anti-Entropy）| [gossip][gossip-visualization] |  | Cassandra， BT(Bit-Torrent)
最终一致性| [raft][raft]  | 相对Paxos简单。主从，三个阶段   | etcd
最终一致性|Master-Slave   |延迟低，吞吐高<br>主动推送/被动拉取  | Mysql 
最终一致性|Master-Master  |延迟低，吞吐高                      | Mysql	
弱一致性||| Backups（备份）

### 1.3 最终一致性 客户端为中心的一致性（Client-centric Consistency）
> 以客户端为中心的一致性为单一客户端提供一致性保证，保证该客户端对数据存储的访问的一致性，但是它不为不同客户端的并发访问提供任何一致性保证.

单调读一致性（Monotonic-read Consistency）
单调写一致性（Monotonic-write Consistency）

### 1.4 可定制的一致性

可定制的一致性|协议|特性|举例
:-:|:-:|:-:|:-:
可定制的一致性|R+W>N[ReadQurum-WriteQurum]| 可定制 | [Dynamo, Cassandra](../../../../2018/07/19/NoSQL/)  定制灵活
	
## 二. 柔性事务 最终一致性

模式 |  流程 | 流程细节 
:-:|:-:|:-:
EBay模式 #2 [8] |  **正向流程**<br> [本地事务+幂等业务接口+half消息] | 消息状态<br> 1. 初始化：消息为待处理状态<br> 2. 业务成功：消息为待发送状态<br>3. 业务失败：消息删除 
 |    **反向流程**（异常流程，补偿流程） | 中间件询问业务执行结果，更新消息状态 
TCC #4|1.主流程控制整个事务 2.分流程提供Confirm和Cancel方法。| Try:  阶段1的业务执行  Confirm: 阶段2的业务执行  Cancel: 回滚Try阶段执行的业务流程和数据
Saga 1PC (一阶段)| 基于补偿的消息驱动的用于解决long-running process业务。 |  a  
补偿 | 状态查询（成功or失败）+补偿| 定时校验异常 + 补偿


模式 | 工程 | 事务seata/Fescar
:-:|:-:|:-:
EBay模式 #2 [8]|  Eg:  阿里Notify | XA, RocketMQ事务消息
TCC #4| Eg: 支付宝DTS #3 |蚂蚁 XTS(内部)/DTX(蚂蚁金融云) #3 <br>**入侵性**<br>  TCC #4 FMT
两阶段 |  | 阿里 TXC(内部)/GTS(阿里云) <br>**非入侵性** <br>[AT Mode][AT Mode] 基于 支持本地 ACID 事务 的 "关系型数据库" <br>[MT Mode][MT Mode] 支持把"自定义"的分支事务纳入到全局事务的管理中
 

## 三. 柔性事务 - 消息

![mq-normal](https://user-images.githubusercontent.com/5608425/66023796-d2d0e680-e524-11e9-8748-1a26f3d0f157.JPG)
![mq-reverse](https://user-images.githubusercontent.com/5608425/66023797-d2d0e680-e524-11e9-85e6-f845863fe4a8.JPG)

<style>
table th:first-of-type {
  width: 100px;
}
</style>

## 四. 时钟

<div style="text-align: center;">
![vectorClock](https://user-images.githubusercontent.com/5608425/67629891-be300600-f8b8-11e9-931c-e0fa265f2f78.jpg)
图1. 向量时钟 vector-clock

![logicClock](https://user-images.githubusercontent.com/5608425/67629944-a9a03d80-f8b9-11e9-820e-7bbf4fccea20.jpg)
图2. 逻辑时钟 logic-clock
</div>

## 五.  state machine && primary-copy

<div style="text-align: center;">
![state-machine-primary-back](https://user-images.githubusercontent.com/5608425/67629999-09e3af00-f8bb-11e9-88fb-10142745bfdd.png)
state machine replication && primary-copy
</div>

> 复制状态机(state machine replication): 多个节点上，从相同的初始状态开始，执行相同的一串命令，产生相同的最终状态
> zk 是 primary-copy的； mysql是primary-copy， slave relay log


## 参考：

### 一致性
1. [保证分布式系统数据一致性的6种方案](https://weibo.com/ttarticle/p/show?id=2309403965965003062676)  高可用架构  good
2. [深入解析NoSQL数据库的分布式算法](https://www.csdn.net/article/2015-01-30/2823782)   good
3. ZooKeeper真不是最终一致性的，而是顺序一致性 陈东明
4. 为什么程序员需要关心顺序一致性（Sequential Consistency）而不是Cache一致性（Cache Coherence） carlosstephen
5. [分布式系统：一致性模型](https://yq.aliyun.com/articles/693187)  阿里 肖汉松 good
6. ENode 1.0 - Saga的思想与实现 汤雪华
7. 《大数据日知录：架构与算法》 张俊林
8. [Base: An Acid Alternative](https://queue.acm.org/detail.cfm?id=1394128)  Ebay模式  good
 
### Fescar && TCC
1. 分布式事务之TCC事务 梁钟霖
2. 分布式事务之TCC服务设计和实现注意事项 绍辉
3. https://github.com/www6v/tcc-transaction
4. [更开放的分布式事务 | Fescar 品牌升级，更名为 Seata](https://mp.weixin.qq.com/s/S0touTyVWfolEqgFaAjLxg)
5. [关于开源分布式事务中间件Fescar，我们总结了开发者关心的13个问题](https://mp.weixin.qq.com/s/XTCZEZdmToWrETbR1GtR4g)

### 应用
1. [数据一致性检测应用场景与最佳实践](https://github.com/StabilityMan/StabilityGuide/blob/master/docs/processing/lostprevention/%E6%95%B0%E6%8D%AE%E4%B8%80%E8%87%B4%E6%80%A7%E6%A3%80%E6%B5%8B%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF%E4%B8%8E%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5.md) 阿里 未

##  向量时钟
1. [向量时钟Vector Clock in Riak](https://www.cnblogs.com/yanghuahui/p/3767365.html)
2. [Why Vector Clocks Are Hard](https://riak.com/posts/technical/why-vector-clocks-are-hard/) 未
3. [Dynamo: Amazon’s Highly Available Key-value Store](http://bnrg.eecs.berkeley.edu/~randy/Courses/CS294.F07/Dynamo.pdf) paper 未
   向量时钟的变种   版本向量（Version vector）  版本控制机制
4. [分布式系统：向量时钟](https://yq.aliyun.com/articles/690584) 阿里 肖汉松  good