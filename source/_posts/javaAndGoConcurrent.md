---
title: Java和Go的并发
date: 2020-04-30 13:02:16
tags:
  - concurrent
  - 并发
categories: 
  - 语言
  - 并发  
---

<p></p>
<!-- more -->

## 一. Java和Go的并发
{% asset_img javaAndgo.JPG Java和Go的并发 %}

{% asset_img threadRelationship.JPG  用户线程和系统线程之间的关系（M:N，1:1） %}


## 二. Goroutine的调度


{% asset_img goroutine.JPG goroutine的调度 %}
**System Thread** : kernel entity
**Processor** : go实现的协程处理器
**Goroutine** : 协程


Processor在不同的系统线程里, 每个Processor挂载着一个协程队列，Processor依次调用Gorouine，
只有一个Gorouine是正在运行状态的。
case1: **一个协程运行时间运行的特别长**。 守护线程计数， 如果processor完成的goroutine数量一直不变。在这个协程的任务栈里插入一个中断的标记，读到这个中断标记后，goroutine会把自己中断下来插入到等待队列的队尾。
case2: **某个协程被io中断后**， Processer会把自己移动到另一个可用的系统线程当中， 执行队列里的任务。 
       当这个协程io中断被唤醒后，会把自己加入到某个Processor的队列里， 或者加入到全局的等待队列里。



## 参考：
[Golang的GMP原理与调度](https://blog.csdn.net/qq_44205272/article/details/111565957)