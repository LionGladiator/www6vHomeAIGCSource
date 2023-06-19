---
title: Golang Concurrency
date: 2022-06-19 12:22:27
tags:
  - Golang
categories:
  - Golang 
  - Concurrency
---

<p></p>
<!-- more -->


## 目录
<!-- toc -->


## 并发 [2][1]
##### 并发原语
+ sync.Once [3]
  - 保证在 Go 程序运行期间的某段代码只会执行一次

+ sync.Pool [3]
  + 两种场景中可以使用Pool做优化
    - 进程中的inuse-objects数过多,  gc mark 消耗大量CPU
    - 进程中的inuse-objects数过多, 进程RSS占用过高
  + 最佳实践
    请求生命周期开始时，pool.Get, 请求结束时, pool.Put

+ semaphore 信号量 [3]
  - 是锁的实现基础, 所有同步原语的基础设施
  - 内部结构
    + treap tree+heap
      - 二插搜索树
        树上的每个节点都是一个链表
      - 小顶堆
     
+ sync.Mutex  互斥锁 [3]
   - 内部结构
     + state
       饥饿模式:  最新进自旋的goroutine优先级最高
       非饥饿模式:  排队
     + sema 信号量
   
+ sync.RWMutex 读写锁 [3]
   - 内部结构
     + w
     + writerSem
     + readerSem
     + readerCount
     + readerWait

+ sync.Map  [3]
  - 线程安全的Map
  - 内部结构
    + mu
    + read
    + dirty
    + misses
  - 设计
    + map+lock 多核扩展性差一点
    + sync.Map 在读多写少的情况下，基本上不需要加锁
    
+ sync.WaitGroup   [3]
  - 等待一组 Goroutine 的返回
  - 内部结构
    + state1

+ sync.Cond
  - 让一组的 Goroutine 都在满足特定条件时被唤醒
 
+ sync.Context
  - 进行上下文信息传递、提供超时和取消机制、控制子 goroutine 的执行

##### 扩展并发原语 
errgroup 
Semaphore
SingleFlight

##### channel
+ channel的读写
  + 向关闭的channel中写入数据会panic
  + 关闭的channel中读数据
    有数据会读到数据，没数据会是nil  

+ buffer channel vs. 非buffered channel  
|    /     |       无缓冲       |        有缓冲         |
| :------: | :----------------: | :-------------------: |
| 创建方式 |  make(chan TYPE)   | make(chan TYPE, SIZE) |
| 发送阻塞 | 数据接收前发送阻塞 |   缓冲满时发送阻塞    |
| 接收阻塞 | 数据发送前接收阻塞 |   缓冲空时接收阻塞    |

[3.Go channel有无缓冲的区别？](https://www.golangroadmap.com/class/gointerview/4-3.html)  


##### Context
进行上下文信息传递、提供超时和取消机制、控制子 goroutine 的执行
``` golang
 func WithCancel(parent Context) (ctx Context, cancel CancelFunc) 
 func WithDeadline(parent Context, d time.Time) (Context, CancelFunc) 
 func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc) 
```

[context](https://pkg.go.dev/context)


## 参考
1. [GOLANG ROADMAP](https://www.golangroadmap.com/class/gointerview/)
  [GOLANG ROADMAP](https://www.golangroadmap.com/)  
  邀请码：caspar 
  邀请码：Gopher-10645-1382
2. 《Go 并发编程实战课》 极客时间  鸟窝
3. 《15 辅导 + 案例分析 + 答疑-更多课程》  体系课_Go高级工程师实战营(完结) 