---
title: Kafka Producer生产者
date: 2022-05-15 23:05:40
tags:
  - kafka  
categories:
  - 消息系统
  - Kafka   
---

<p></p>
<!-- more -->


## Producer Arch
### Overviw 1 [2]
{% asset_img  kafka-producer1.jpg   Producer架构 %}

整个生产者客户端是由主线程和Sender线程协调运行的, 主线程创建消息, 然后通过 拦截器、元信息更新、序列化、分区器、缓存消息等等流程。
Sender线程在初始化的时候就已经运行了,并且是一个while循环。

### Overviw 2
{% asset_img   kafka-producer.jpg  Producer架构 %}



## 参考
1. [图解kafka生产者流程,超详细！](https://mp.weixin.qq.com/s?__biz=Mzg4ODY1NTcxNg==&mid=2247493639&idx=1&sn=4bd43c8137a701e73dddd9ccdf58ad45&c) 未
