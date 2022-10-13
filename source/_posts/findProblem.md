---
title: 故障排查的流程和方法
date: 2019-07-06 17:18:47
tags:
  - 故障排查  
categories:
  - 故障排查  
  - 流程和方法 
---

<p></p>
<!-- more -->

## 故障处理通用流程 [4]
+ 故障处理过程中效率如何，其实取决于三个因素：
  + 技术层面的故障隔离手段是否完备； [隔离] [技术架构]
  + 故障处理过程中的指挥体系是否完善，角色分工是否明确； [角色][组织架构]
  + 故障处理机制保障是否经过足够的演练。 [演练][事前]

## 微服务 故障快速定位问题 [3]
1. 打印日志中的异常
   RPC 异常列表、 异常码、 异常码形成异常列表 

{% asset_img errorCode.PNG 异常码 %}
   
2. 借助分布式链路跟踪 APM


## 参考
1. [微服务](/2019/09/09/microservice/)  self
2. [可观察性 链路追踪](/2019/08/31/observability/)   self
3. [RPC实战与核心原理进 - 19 | 分布式环境下如何快速定位问题？]()   何小锋
4. 07｜故障处理：一切以恢复业务为最高优先级
5. [《SRE google 运维解密》读书笔记 （二）](https://cloud.tencent.com/developer/article/2010401) 未
6. [《SRE google 运维解密》读书笔记 （三）](https://cloud.tencent.com/developer/article/2010405)
7. [《SRE google 运维解密》读书笔记 （四）](https://cloud.tencent.com/developer/article/2010408)
 


