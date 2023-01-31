---
title: DevOps
date: 2022-01-29 16:45:04
tags: 
  - devops
categories:
  - devops
---

<p></p>
<!-- more -->

## Devops Roadmap

{% asset_img devopsRoadmap.png  DevopsRoadmap %}

## DevOps
一种理念，文化

## Ops
##### 配置管理 [5]
+ 四个理念
  - 版本变更标准化，
  - 将一切纳入版本控制，
  - 全流程可追溯和
  - 单一可信数据源

##### CI/CD pipeline
+ CI/CD
  - jenkins
  - github actions
+ GitOps  
  - argoCD

##### GitOps [6]
{% asset_img  gitOps.JPG GitOps  %} 

##### IaC
+ Config Management
  - ansible
  - salt
+ infra Provisioning
  - Terraform

## Dev
##### 分支策略 [4]
+ 主干开发，分支发布
+ 分支开发，主干发布
+ 主干开发，主干发布
  大道至简

##### 本地开发 [6]
KSync
Skaffold-谷歌
Nocalhost-腾讯CODING

##### 技术债务 [7]
{% asset_img  debt.JPG  技术债务  %}

## Monitor[self]
+ Metric 
+ APM
+ Log

## DevSecOps

## 参考
1. [Argo CD 保姆级入门教程](https://cloud.tencent.com/developer/article/2153864)  云原生实验室 未
2. [大妈都能看懂的 GitOps 入门指南](https://cloud.tencent.com/developer/article/2153852)  云原生实验室 未
3. [利用服务网格为基于微服务的应用程序实施 DevSecOps](https://lib.jimmysong.io/service-mesh-devsecops/)  jimmy song 未
4. [11 | 分支策略：让研发高效协作的关键要素]() 石雪峰
5. [10 | 配置管理：最容易被忽视的DevOps工程实践基础]() 石雪峰
6. [16 | 环境管理：一切皆代码是一种什么样的体验？ ]() 石雪峰
7. [15 | 技术债务：那些不可忽视的潜在问题]() 石雪峰



