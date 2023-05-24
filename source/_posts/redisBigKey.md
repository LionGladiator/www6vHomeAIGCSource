---
title: Redis 大Key
date: 2021-05-21 07:33:33
tags:
  - Redis
categories:
  - 数据库  
  - KV
  - Redis 
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

# Redis 大Key
### 什么是 Bigkey [5]
【字符串类型】： 单个string类型的value值超过1MB，就可以认为是Bigkey。
【非字符串类型】：哈希、列表、集合、有序集合等， 它们的元素个数超过2000个，就可以认为是Bigkey。

### 问题 

问题1 [1] 
+ 大key在数据迁移[遇到过]、扩容、删除[遇到过]时会有卡顿。 

问题2 [3]
+ 执行大key命令的客户端本身，耗时明显增加，甚至超时
+ 执行大key相关读取或者删除操作时，会严重占用带宽和CPU，影响其他客户端
+ 大key本身的存储带来分布式系统中分片数据不平衡，CPU使用率也不平衡
+ 大key有时候也是热key，读取操作频繁，影响面会很大
+ 执行大key删除时，在低版本redis中可能阻塞线程   [遇到过]

###  查找 大Key
+ --bigkeys参数,   用scan扫描大key  [1][2][5]
``` Shell
$ redis-cli --bigkeys
# Scanning the entire keyspace to find biggest keys as well as
# average sizes per key type.  You can use -i 0.1 to sleep 0.1 sec
# per 100 SCAN commands (not usually needed).
[00.00%] Biggest zset   found so far 'testzset' with 129 members
[00.00%] Biggest hash   found so far 'h2' with 513 fields
[00.00%] Biggest set    found so far 'si1' with 5 members
[00.00%] Biggest hash   found so far 'h4' with 514 fields
[00.00%] Biggest string found so far 'key' with 9 bytes
-------- summary -------
Sampled 9 keys in the keyspace!
Total key length in bytes is 27 (avg len 3.00)
Biggest string found 'key' has 9 bytes
Biggest    set found 'si1' has 5 members
Biggest   hash found 'h4' has 514 fields
Biggest   zset found 'testzset' has 129 members
1 strings with 9 bytes (11.11% of keys, avg size 9.00)
0 lists with 0 items (00.00% of keys, avg size 0.00)
2 sets with 8 members (22.22% of keys, avg size 4.00)
4 hashs with 1541 fields (44.44% of keys, avg size 385.25)
2 zsets with 132 members (22.22% of keys, avg size 66.00)
0 streams with 0 entries (00.00% of keys, avg size 0.00)
```
  +  注意点
     - **建议在slave节点执行**，因为--Bigkeys也是通过scan完成的，可能会对节点造成阻塞。
     - **建议在节点本机执行**，这样可以减少网络开销。
     - 如果没有从节点，**可以使用--i参数**，例如(--i 0.1 代表100毫秒执行一次)。
     - --Bigkeys只能计算每种数据结构的top1，如果有些数据结构有比较多的Bigkey，是查找不出来的。

+ 离线分析RDB [3]
  使用redis-rdb-tools离线分析工具来扫描RDB持久化文件
  
  [redis-rdb-tools](https://github.com/sripathikrishnan/redis-rdb-tools)

### 解决方案 [3]
+ 可删除
  - 渐进式删除<4.0版本
    **scan命令  游标式迭代扫描**
    使用 hscan、sscan、zscan 方式渐进式删除 [4]
  - 惰性删除>4.0版本
    **unlink命令  异步惰性非阻塞删除**
     ``` 
     unlink <keyName> 
     ```
  
+ 不可删除
  - 拆分
    - 字符串类型
      比如商品信息，根据的类别拆分 key。
    - 集合类元素
      按照日期拆分，key20220101、key20220102    
  - 压缩 Value 数据  
    使用序列化、压缩算法将 Key 的大小控制在合理范围内，但是需要注意，序列化、反序列化都会带来一定的消耗

# 参考
1. 《Redis 深度历险：核心原理与应用实践》 钱文品
    7. 大海捞针 ——— scan  
2. [Redis中查找大key](https://segmentfault.com/a/1190000018193214?utm_source=tag-newest)
3. [解决了Redis大key问题](https://zhuanlan.zhihu.com/p/473930220)  *** 
   [解决了Redis大key问题](https://mp.weixin.qq.com/s/0WS7_9EIQqpYlUNWgZZJog)
4. [Redis 大 key 要如何处理？](https://blog.csdn.net/qq_34827674/article/details/126225192)
5. [Bigkey问题的解决思路与方式探索](https://zhuanlan.zhihu.com/p/584594738) vivo team *** 