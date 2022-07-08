---
title: Ceph
date: 2022-01-08 21:50:33
tags:
  - 存储
categories: 
  - 分布式
  - 存储
  - ceph  
---

<p></p>
<!-- more -->

## ceph架构图
{% asset_img   ceph-arch.png   ceph架构图 %}

{% asset_img   ceph-arch1.png   ceph架构图 %}


## Ceph核心组件及概念介绍
+ Monitor
    监控整个集群的状态，维护集群的cluster MA二进制表，保证集群数据的一致性
+ OSD
    OSD全程Object storage Device,也就是负责响应客户端请求返回具体数据的进程。一个Ceph集群一般都有很多个OSD
+ MDS
    MDS全称Ceph Metadata Server，是CephFS服务依赖的元数据服务
+ Object
    Ceph最底层的存储单元是Object对象，每个object包含数据和原始数据。
+ PG
    PG全称Placement Grouops,是一个逻辑的概念，一个PG包含多个OSD。引入PG这一层其实是为了更好的分配数据和定位数据。
+ RADOS
    RADOS全称Reliable Autonomic Distrubuted object Store,是Ceph集群的精华，用户实现数据分配，Failover等集群操作
+ Libradio    
    Librados是RODOS提供库，因为RADOS是协议很难直接访问，因此上层的RBD、RGW和CephFS都是通过librados访问的，目前提供PHP、Ruby、Java、Python、C和C++支持。
+ CRUSH
    CRUSH是Ceph使用的数据分布算法，类似一致性哈希，让数据分配到预期的地方。
+ RBD
    RBD全称RADOS Block Device，是ceph对外提供服务的块设备服务。
+ RGW
    RGW全称RADOS gateway,是ceph对外提供的对象存储服务，接口与S3和Swift兼容
+ CephFS
    CephFS全称Ceph File System，是ceph对外提供的文件系统服务。


## 参考
[「基础理论」CEPH 基础介绍](https://github.com/0voice/kernel_awsome_feature/blob/main/%E3%80%8C%E5%9F%BA%E7%A1%80%E7%90%86%E8%AE%BA%E3%80%8DCEPH%20%E5%9F%BA%E7%A1%80%E4%BB%8B%E7%BB%8D.md) good
[分布式存储Ceph](https://github.com/0voice/kernel_awsome_feature/blob/main/%E5%88%86%E5%B8%83%E5%BC%8F%E5%AD%98%E5%82%A8Ceph.md)
[分布式文件系统ceph知识整理](https://github.com/0voice/kernel_awsome_feature/blob/main/%E5%88%86%E5%B8%83%E5%BC%8F%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9Fceph%E7%9F%A5%E8%AF%86%E6%95%B4%E7%90%86.md)
[分布式存储 Ceph 的演进经验 · SOSP '19](https://github.com/0voice/kernel_awsome_feature/blob/main/%E5%88%86%E5%B8%83%E5%BC%8F%E5%AD%98%E5%82%A8%20Ceph%20%E7%9A%84%E6%BC%94%E8%BF%9B%E7%BB%8F%E9%AA%8C%20%C2%B7%20SOSP%20'19.md)
[马哥教育2021-Ceph分布式存储系统快速入门](https://www.bilibili.com/video/BV17p4y1a7Em?p=4&vd_source=f6e8c1128f9f264c5ab8d9411a644036)