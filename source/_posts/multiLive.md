---
title: 异地多活总结
date: 2017-06-17 22:42:53
tags:
  - 异地多活
categories:
  - 架构 
  - 系统架构 
  - 异地多活  
---

<p></p>
<!-- more -->

{% asset_img  multiLive.jpg  异地多活总结  %}


---------
异地多活
	阿里
		基于Userid的单元化异地多活
		主要改造整个交易链路
		交易链路（单元）和非交易链路（中心）之间通过DRC同步数据。单元里的数据是全量、只读的
	饿了么
		思路+原则
			基于地理位置的异地多活。用户、商家、骑手都会在相同的机房
			可用性优先，放宽数据一致性
		主要组件
			GZS（元数据）+APIRouter（流量路由）
			SOA Proxy：内部网关、IDC之间调用
			Data Replication Center：数据库复制、数据库和cache之间的一致性
			Data Access Layer
			zk,mq在IDC之间的同步
	数据一致性
		强一致场景
			都读主节点
		最终一致性场景
			DRC异步同步数据
			业务层异步分发数据
		数据丢失
			通过算法在不同机房都能生成相同的

----------


## 参考
1. 《尽在双11:阿里巴巴技术演进与超越》 1.2节
2. [饿了么异地多活技术实现（一）总体介绍](https://zhuanlan.zhihu.com/p/32009822)   饿了么框架工具部  知乎专栏
3. [SET化架构设计](https://blog.csdn.net/lql_h/article/details/95588996)  lql_h  未
