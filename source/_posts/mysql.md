---
title: Mysql
date: 2019-09-10 16:02:39
tags:
  - mysql
categories:  
  - mysql
---

<p></p>
<!-- more -->

## 一. 索引
 index | MyISAM  |  InnoDB | Memory
:-: | :-: | :-: | :-:
 B-Tree<br>（balanced 平衡的）  | 支持 | 支持  | 支持
 Hash        | 不支持 | 不支持  | 支持
 R-Tree <br>空间索引 |  支持| 不支持 | 不支持
 Full-text    | 支持| 支持 | 不支持



 index使用场景 | 例子
 :-: | :-: 
 匹配全值  |  index (a,b,c) <br> a=1 and b=2 and c=3
 范围查找  |  index a>1 and b<3 
 匹配最左前缀  |  index(a，b，c)  <br> a OR a，b OR a、b、c OR a，c 会使用 <br>  b、c 不使用
 仅对索引列进行查询（覆盖索引）  |  index  a <br> a=1
  匹配列前缀 | index （a， b） <br> a like 'WEER%'    
  Index Condition Pushdown（ICP） |  减少回表IO     


 不会使用index的场景 | 例子
 :-: | :-: 
 like | like "%NI%"
 隐式转换 | lastname=1  不使用索引 <br>lastname='1'  使用索引
  复合索引不符合最左匹配原则 |
 or | index a <br>  a=3 or c=6 or d=9

<div style="text-align: center;">
![innodb-myisam-index](https://user-images.githubusercontent.com/5608425/65374438-66501f00-dcbc-11e9-8609-2da27df96809.png)
Innodb和MyISAM中的聚集索引和非聚集索引(二级索引)
</div>

## 二. redo log 和 undo log
```
  事务开始.
      记录A=1到undo log.
      修改A=3.
      记录A=3到redo log.( 先写内存， 后同步到磁盘中)

      记录B=2到undo log.
      修改B=4.
      记录B=4到redo log.( 先写内存， 后同步到磁盘中)

      将redo log写入磁盘。
  事务提交
```

## 三. MyISAM 和 InnoDB

  描述  | MyISAM  |  InnoDB 
  :-: | :-: | :-: 
  行锁(并发高，会死锁)| × | √ (默认支持)<br>Record lock: 锁记录<br>Gap lock: 锁范围，不锁记录<br>Next-key lock： 锁范围+锁记录
  表锁(并发低，不会死锁)| √ |  √
事务和崩溃恢复| × |  √
外键| × |  √
MVCC| × |  √ <br> 在READ COMMITTED 和 REPEATABLE READ时有效 

> innodb对于行的查询使用next-key lock
  Next-locking keying、Gap锁为了解决Phantom Problem幻读问题
  当查询的索引含有唯一属性时(单条记录)，将next-key lock降级为record key


## 参考:

1. 《深入浅出MySQL：数据库开发、优化与管理维护》 
2. [MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)
3. [理解MySQL——索引与优化](https://www.cnblogs.com/hustcat/archive/2009/10/28/1591648.html)
4. [Mysql事务总结](../../../../2015/02/21/transaction/) self
5. [剖析Mysql的InnoDB索引](https://blog.csdn.net/voidccc/article/details/40077329)  good
6. [可能是全网最好的MySQL重要知识点](https://mp.weixin.qq.com/s/M1dLLuePpdM9vA3F1uJGyw)