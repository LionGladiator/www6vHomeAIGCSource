---
title: Kubenetes总结
date: 2019-04-25 10:29:34
tags:
  - Kubenetes
categories:
  - 分布式 
  - 云原生
  - Kubernetes  
---

{% asset_img   Kubenetes.jpg  Kubenetes  %}

<!-- more -->

{% asset_img   kubernetes-high-level-component-archtecture.jpg  kubernetes-high-level-component-archtecture  %}

{% asset_img   k8s-tech.jpg  k8s技能图谱  %}


||控制器|
|:---:|:---:|
|Replication Controller<br> ReplicaSet<br> Deployment|无状态| 
|StatefulSet<br> PersistentVolumeController|有状态| 
| Servcie【1】<br>Namespace | x |

## 参考:

1. [《Kubenetes in Action》七牛容器云团队](http://product.dangdang.com/26439199.html?ref=book-65152-9168_1-529800-3)
2. [云原生](https://www6v.github.io/www6vHomeHexo/2019/05/02/cloudNative/)
3. [Kubernetes架构](https://jimmysong.io/kubernetes-handbook/concepts/)
4. [面向 Kubernetes 编程： Kubernetes 是下一代操作系统](https://mp.weixin.qq.com/s/E5-agHtMvW_X7znVJDkTKA)
5. [K8S 从懵圈到熟练：读懂此文，集群节点不下线](https://mp.weixin.qq.com/s/xmVHqTZblQsAxdaSjWjn1Q)
6. [课时4 理解 Pod 和容器设计模式](https://edu.aliyun.com/lesson_1651_13079?spm=5176.254948.1334973.10.2c12cad2AHzzTw#_13079)
7. [课时3 Kubernetes 核心概念](https://edu.aliyun.com/lesson_1651_13078?spm=5176.254948.1334973.8.2c12cad2AHzzTw#_13078)
8. [课时5 应用编排与管理：核心原理](https://edu.aliyun.com/lesson_1651_13080?spm=5176.254948.1334973.12.2c12cad2AHzzTw#_13080)
9. [课时6 应用编排与管理： Deployment](https://edu.aliyun.com/lesson_1651_13081?spm=5176.10731542.0.0.e7a120beywNIVX#_13081)
10. [深入剖析Kubernetes - 16  编排其实很简单：谈谈“控制器”模型 张磊]()
