---
title: Linux 进程
date: 2019-08-22 15:46:10
tags:
  - linux
categories:
  - linux
  - kernel 
  - 进程  
---

<p></p>
<!-- more -->

<div style="text-align: center;">

![进程执行](https://user-images.githubusercontent.com/5608425/63564515-e6894480-c597-11e9-90c2-eba751ad0c08.jpg) 进程执行

</div>

## 内核初始化
+ Systemd 1号进程在用户态将运行一个用户进程, 1号进程和子进程形成一棵进程树。
+ 内核态2号进程： 使用 kernel_thread 函数创建进程。  线程 == 轻量级进程
  

## 进程间通信

+ 管道，命令行中常用的模式
+ 消息队列其实很少使用，因为有太多的用户级别的消息队列，功能更强大。
+ 共享内存加信号量是常用的模式。这个需要牢记，常见到一些知名的以C语言开发的开源软件都会用到
它。
+ 信号更加常用，机制也比较复杂。


## 参考：
+ [趣谈Linux操作系统 - 08_内核初始化：生意做大了就得成立公]()    刘超
+ [趣谈Linux操作系统 - 10_进程：公司接这么多项目，如何管]()   刘超
+ [趣谈Linux操作系统 - 36-进程间通信：遇到大项目需要项目组之间的合作才行]() 刘超









 