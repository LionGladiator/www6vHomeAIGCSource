---
title: AWS Network
date: 2022-10-01 06:14:02
tags:
  - 云计算
categories:
  - 云计算  
  - AWS
---

<p></p>
<!-- more -->

## Overview[1][3]
{% asset_img   awsNetwork.jpg AWS网路产品 %}

{% asset_img   aws-network-compare.JPG  VPC Peering vs. Transit VPC vs. Transit Gateway  %}



+ vpc peering ***
  - [劣势：没有transitive 特性]
+ transit gateway ***
  - [劣势： 不能跨region. 如果要跨region, 需要TGW之间做peering] 
+ transit vpc(解决方案)
+ private link
  - vpc上打个洞，用的比较少
+ 专线
  + direct connect ***
+ vpn
  + client vpn ***
  + vpn gateway  
  + vpn site-to-site *** 
    [AWS Site-to-Site VPN](https://zhuanlan.zhihu.com/p/395805857)
    [AWS Site-to-Site VPN 的工作原理](https://docs.aws.amazon.com/zh_cn/vpn/latest/s2svpn/how_it_works.html)
+ sd-wan    
  + cloudwan  

## VPC
+ CIDR & subnet
+ Routing Table
+ Security Groups & Network ACLs
  + Security Groups
    EC2 level
  + Network ACLs
+ NAT Gateway    


## 参考
+ overview
1.[亚马逊云科技企业组网解决方案 | 一期一会](https://www.bilibili.com/video/BV1gQ4y1k7LH/)


+ vpc
2.[010-计算机网络-无分类编址CIDR](https://www.bilibili.com/video/BV1Ff4y1S7Lf/)
3.[【云计算】AWS高级网络.LAB1.1.vpc_peering](https://www.bilibili.com/video/BV1CG41137bx/)
4.[UCloud 陈煌栋-UCloud VPC的技术演进之路]() 
[VPC](https://jayendrapatil.com/aws-virtual-private-cloud-vpc/) *** 
[路由表概述](https://help.aliyun.com/document_detail/106224.html) 阿里云 有例子


+ Tool
https://network00.com/NetworkTools/IPv4SubnetCreator/
