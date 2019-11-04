---
title:  Kubernetes服务
date: 2019-11-04 11:56:32
tags:
  - Kubernetes
categories:
  - 分布式 
  - 云原生
  - Kubernetes
---

<p></p>
<!-- more -->

## 一. Kubenetes服务

<div style="text-align: center;">
![Kubernetes服务发现架构](https://user-images.githubusercontent.com/5608425/68105556-d4257280-ff19-11e9-9d19-f18f5c454f34.jpg)
Kubernetes服务发现架构
</div>

{% asset_img   k8sService.jpg  Kubernetes服务  %} 

### 1.1 通过DNS发现服务
> 每个Service对象相关的DNS记录有两个：
{SVCNAME}.{NAMESPACE}.{CLUSTER_DOMAIN}
{SVCNAME}.{NAMESPACE}.svc.{CLUSTER_DOMAIN}

```
root@kubia-9nvx7:/# cat /etc/resolv.conf
nameserver 172.17.0.2
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

```
（1）拥有ClusterIP的Service资源，要具有以下类型的资源记录
A记录：<service>.<ns>.svc.<zone>. <ttl>  IN  A  <cluster-ip>
（2）Headless类型的Service资源，要具有以下类型的资源记录
A记录：<service>.<ns>.svc.<zone>. <ttl> IN A <endpoint-ip>
（3）ExternalName类型的Service资源，要具有CNAME类型的资源记录
CNAME记录：<service>.<ns>.svc.<zone>. <ttl> IN CNAME <extname>.
```

补遗:
1. Service中的ClusterIP是VIP, 虚拟IP

## 二. Kubenetes服务 -> kube-proxy

+ kube-proxy的作用: 网络配置

##### userspace 代理模式
<div style="text-align: center;">
<img width="450" alt="user-space-proxy" src="https://user-images.githubusercontent.com/5608425/68077955-2b3b2280-fe08-11e9-8672-3210219a7372.png">
userspace 代理模式
</div>

##### iptables Proxy Mode
<div style="text-align: center;">
<img width="450" alt="iptables-proxy" src="https://user-images.githubusercontent.com/5608425/68077954-2b3b2280-fe08-11e9-8231-cb9bc177ba21.png">
 Iptables Proxy Mode
</div>

##### IPVS proxy mode
> IPVS是LVS一个组件，提供高性能、高可靠性的四层负载均衡器。IPVS 是IP Virtual Server的简写。IPVS构建在netfilter上，作为Linux 内核的一部分，从传输层实现了负载均衡。
 

## 参考:
1. [深入理解 Kubernetes之：Service](https://www.kubernetes.org.cn/5992.html) good
2. [第14 章 ： Kubernetes Services](https://edu.aliyun.com/lesson_1651_17064#_17064) 
3. [《Kubenetes in Action》](http://product.dangdang.com/26439199.html?ref=book-65152-9168_1-529800-3)  七牛容器云团队
4. [Kubernetes中的服务发现机制与方式](https://mp.weixin.qq.com/s/3THiWFt52tZckFGxg3Cx-g) 马永亮 