---
title: istio数据面
date: 2019-11-21 17:05:08
tags:
  - istio
categories: 
  - 云原生
  - serviceMesh  
---

<p></p>
<!-- more -->

##  sidecar注入和路由转发
##### sidecar注入
{% asset_img  istio-data.jpg  istio数据面-sidecar注入 %}

##### Envoy Sidecar 代理的路由转发 [7][8]
<div style="text-align: center;">

![Envoy Sidecar 代理的路由转发](https://user-images.githubusercontent.com/5608425/64623499-a50fea80-d41b-11e9-9524-6d834fd45d88.jpg)  Envoy Sidecar 代理的路由转发
</div>

参考:
7. [理解 Istio Service Mesh 中 Envoy 代理 Sidecar 注入及流量劫持](https://jimmysong.io/posts/envoy-sidecar-injection-in-istio-service-mesh-deep-dive/)  宋净超
8. [理解 Istio Service Mesh 中 Envoy Sidecar 代理的路由转发](http://www.servicemesher.com/blog/envoy-sidecar-routing-of-istio-service-mesh-deep-dive/)  宋净超  引
21. [《云原生服务网格Istio：原理、实践、架构与源码解析》](https://item.jd.com/12538407.html) 第6章
22. [Istio Sidecar 注入过程解密](https://preliminary.istio.io/zh/blog/2019/data-plane-setup/) istio官方

##  Envoy proxy的架构 [18]
<div style="text-align: center;">

![Envoy代理的架构](https://user-images.githubusercontent.com/5608425/64623492-a3462700-d41b-11e9-8e2b-6fc0b05d8c5d.jpg)  
Envoy proxy的架构

</div>

参考:
18. [Istio 的数据平面 Envoy Proxy 配置详解](https://www.servicemesher.com/blog/envoy-proxy-config-deep-dive/)  宋净超 引
1. [Istio流量管理实现机制深度解析](https://zhaohuabing.com/post/2018-09-25-istio-traffic-management-impl-intro/)  赵化冰

##  数据面标准API/xDS协议
{% asset_img  istioXDS.jpg  istio-xDS %}

+ pilot和envoy之间的接口
+ xDS是一类发现服务的总称，包含LDS，RDS，CDS，EDS以及 SDS。
+ XDS服务接口的最终一致性: 遵循 make before break 模型

<div style="text-align: center; width: 50%; height: 50%">
	
![xds](https://user-images.githubusercontent.com/5608425/69417029-dded0980-0d52-11ea-96a1-4c14e08aadf8.jpg)
</div>

参考:
17. [xDS 协议解析](https://jimmysong.io/istio-handbook/concepts/envoy-xds-protocol.html)  宋净超  引
1. [Istio流量管理实现机制深度解析](https://zhaohuabing.com/post/2018-09-25-istio-traffic-management-impl-intro/)  赵化冰
2. [Service Mesh——后 Kubernetes 时代的微服务](https://www.servicemesher.com/blog/service-mesh-the-microservices-in-post-kubernetes-era/)


---


## 数据面
##### Istio 注入sidecar实现:
+ 自动注入: 利用 Kubernetes Dynamic Admission Webhooks 对 新建的pod 进行注入: init container + sidecar
  [一种灵活注入 Istio Sidecar 的方案探索](https://cloudnative.to/blog/istio-sidecar-injection-method/)
+ 手动注入: 使用 istioctl kube-inject

##### 注入Pod内容
+ istio-init: 
   通过配置iptables来劫持Pod中的流量。
   Init 容器初始化完毕后就会自动终止，但是 Init 容器初始化结果(iptables)会保留到应用容器和 Sidecar 容器中.
+ istio-proxy: 
   两个进程pilot-agent和envoy, pilot-agent 进行初始化并启动envoy. 【3.2节】

## Envoy启动过程和配置
<div style="text-align: center;">
	
![envoy-config-init](https://user-images.githubusercontent.com/5608425/69950671-b81feb80-152e-11ea-96ba-6261b7f4c09f.png)
Envoy启动过程和配置
</div>

> Envoy 配置热更新: 配置的动态变更，而不需要重启 Envoy.

参考:
[Istio 庖丁解牛1：组件概览](https://mp.weixin.qq.com/s/VwqxrZsVmn4a5PcVckaLxA)  腾讯云 钟华
[istio数据面](../../../../2019/11/21/istioDataplane/) self





