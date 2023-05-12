---
title: MySQL 事务-隔离性和锁
date: 2020-08-14 17:22:20
tags:
  - mysql
categories:  
  - 数据库
  - 关系型
  - mysql
---

<p></p>
<!-- more -->

## 目录
<!-- toc -->

#  MyISAM vs. InnoDB

  描述  | MyISAM  |  InnoDB 
  :-: | :-: | :-: 
  行锁(并发高，会死锁)| × | √ (默认支持)<br>Record lock: 锁记录<br>Gap lock: 锁范围，不锁记录<br>Next-key lock： 锁范围+锁记录
  表锁(并发低，不会死锁)| √ |  √
  事务和崩溃恢复| × |  √
  外键| × |  √
  MVCC| × |  √ <br> 在READ COMMITTED 和 REPEATABLE READ时有效 


#  事务隔离级别  [4]
隔离级别(从高到低)| 脏读|  不可重复读<br>（重点是修改）| 幻影读<br>（重点是新增或者删除）
:-: | :-: | :-: | :-:
SERIALIZABLE| × | × | × 
REPEATABLE-READ<br>（**InnoDB默认隔离级别**）| × | × | √ 
READ-COMMITTED| × | √ | √ 
READ-UNCOMMITTED| √ | √ | √ 

> innodb对于行的查询使用next-key lock
  **Next-locking keying、Gap锁为了解决Phantom Problem幻读问题**
  当查询的索引含有唯一属性时(单条记录)，将next-key lock降级为record key


### 新的隔离级别
+ SI[Snapshot Isolation]
  Oracle 可串行化,  PG和MySQL称为RR
+ SSI[Serializable Snapshot Isolation]
  PostgreSQL 和 CockroachDB 已经支持 SSI


### RC和RR隔离级别 [chat]

下面是一个表格，归纳了以上文字中RC和RR隔离级别的特点：

| 隔离级别 | 快照读 #1 | 当前读 #2                  | 幻读   |
| -------- | --------- | -------------------------- | ------ |
| RC       | 不加锁    | 加锁[记录锁 是,间隙锁 否]  | 存在   |
| RR       | 不加锁    | 加锁[记录锁 是, 间隙锁 是] | 不存在 |


在RC隔离级别下，快照读和当前读都不会对记录加锁，因此不会阻塞其他事务的读操作。但是，由于RC隔离级别只对读取到的记录加锁，而不对读取的范围加锁，因此可能会出现幻读现象。幻读指的是，在一个事务中先后进行两次相同的查询操作，第二次查询会发现有新增的记录，这种现象是由于其他事务在事务中新增了这些记录所导致的。

在RR隔离级别下，快照读和当前读都不会对记录加锁，但是会对读取的范围加锁，防止其他事务在该范围内插入新的记录。因此，在RR隔离级别下不存在幻读现象。

需要注意的是，虽然RR隔离级别可以避免幻读现象，但是由于对读取范围加锁可能会导致性能问题，因此在实际应用中需要根据具体情况选择合适的隔离级别。
[ 当前读   加锁，快照读  不加锁 ]

#  MVCC 
### 原理  [2][3]
{% asset_img  mvcc.JPG  MVCC（一致性读视图） %}

  InnoDB 中的 **RC(READ COMMITTED) 和 RR(REPEATABLE READ) 隔离事务**是基于**多版本并发控制（MVVC）**实现高性能事务。
  **MVCC 对普通的 Select 不加锁**，如果读取的数据正在执行 Delete 或 Update 操作，这时读取操作不会等待排它锁的释放，而是**直接利用 MVCC 读取该行的数据快照**（数据快照是指在该行的之前版本的数据，而数据快照的版本是基于 undo 实现的，undo 是用来做事务回滚的，记录了回滚的不同版本的行记录）。

  **MySQL默认的事务隔离级别是RR(REPEATABLE READ)**, InnoDB引擎的Select操作使用一致性非锁定读（MVCC）。 对于一致性非锁定读， 即使读取的行已经被执行了select...for update,也是可以读取的。

###  实现 
| 当前读/快照读 | 含义                                                         | 例子                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 当前读 #2     | 读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。 | `select ... lock in share mode`(共享锁)，<br>`select ...for update`、<br>`update`、`insert`、`delete`(排他锁) |
| 快照读 #1     | 简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。 | Read Committed：每次select，都生成一个快照读。<br> Repeatable Read：开启事务后第一个select语句才是快照读的地方。<br> Serializable：快照读会退化为当前读。 |



+ MVCC实现 [0]
  + 隐藏字段 
    DB_TRX_ID:  最近修改事务ID
    DB_ROLL_PTR: 回滚指针
    DB_ROW_ID: 隐藏主键    
  + undolog版本链 
    链表的头部是最新的旧记录，链表尾部是最早的旧记录
  + readview
    - ReadView（读视图）是 **快照读** SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务（未提交的）id。
    - 不同的隔离级别，生成ReadView的时机不同：
      - READ COMMITTED ：在事务中每一次执行快照读时生成ReadView。
      - REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。

#  锁
###  行锁， 锁优化 [3]
+ 在InnoDB事务中，**行锁**是在需要的时候才加上的，但并不是不需要了就立刻释放，而是要等到事务结束时才释放。这个就是**两阶段锁协议**。
知道了这个设定，对我们使用事务有什么帮助呢？那就是，**如果你的事务中需要锁多个行，要把最可能造成锁冲突、最可能影响并发度的锁尽量往后放.**[todo 加个例子]

+ **行锁是通过索引实现的**，如果不通过索引条件检索数据，那么 InnoDB 将对表中所有的记录进行加锁。

+ **行锁**的具体实现算法有三种：record lock、gap lock 以及 next-key lock。
  - **record lock**是专门对索引项加锁；
  - **gap lock** 是对索引项之间的间隙加锁；
  - **next-key lock** 则是前面两种的组合，对索引项以其之间的间隙加锁。
  只在可重复读或以上隔离级别下的特定操作才会取得 gap lock 或 next-key lock，在Select 、Update 和 Delete 时，除了基于唯一索引的查询之外，其他索引查询时都会获取gap lock 或 next-key lock，即锁住其扫描的范围。

###  死锁和死锁检测 [5]
当出现死锁以后，有两种策略：
+ 一种策略是，直接进入等待，直到超时。这个超时时间可以通过参数
**innodb_lock_wait_timeout**来设置。
innodb_lock_wait_timeout的默认值是50s。 实际中不用这种策略。

+ 另一种策略是，发起**死锁检测**，发现死锁后，主动回滚死锁链条中的某一个事务，让其他事
  务得以继续执行。将参数 **innodb_deadlock_detect** 设置为on，表示开启这个逻辑。
   - 带来的问题：每个新来的被堵住的线程，都要判断会不会由于自己的加入导致了死锁，这是一个时间复杂度是O(n)的操作。假设有1000个并发线程要同时更新同一行，那么死锁检测操作就是100万这个量级的。虽然最终检测的结果是没有死锁，但是这期间要**消耗大量的CPU资源**。
  
   - 一种解决思路是**控制并发度**：并发控制要做在数据库服务端。如果有中间件，可以考虑在中间件实现；如果-团队有能修改MySQL源码的人，也可以做在MySQL里面。基本思路就是，**对于相同行的更新，-在进入引擎之前排队**。这样在InnoDB内部就不会有大量的死锁检测工作了。
   - 另一种解决思路是**在应用层上优化**:你可以考虑通过将一行改成逻辑上的多行来减少锁冲突。 比如，一个账户1条记录变10条记录。

### 预防死锁 [7]
+ 减少长事务
+ 大事务拆成小事务
+ 保证加锁顺序一直
+ 业务允许的情况下，降低隔离级别
  RR几倍下会有间隙锁，会提高死锁发生的概率
  
### 死锁的排查和解决 [7]
+ 通过日志系统及时**通知**死锁事件
   通过ELK做通知
+ 结合业务代码与**死锁日志** 进行分析
  - 通过 pt-deadlock-logger 监控死锁 
  - 查看最近一次的死锁日志   
    ```show engine innodb status```

###   隐式锁和显示锁
显示锁
SELECT ... LOCK IN SHARE MODE(加共享锁);
SELECT ... FOR UPDATE(加排他锁);

# 参考

0. [黑马程序员 MySQL数据库入门到精通](https://www.bilibili.com/video/BV1Kr4y1i7ru?p=78)  P141-P144
   [mysql_note](https://github.com/www6v/mysql_note) 笔记1
   [MySQL 索引](https://frxcat.fun/database/MySQL/MySQL_Advanced_index/) 笔记2 ***

1. 《深入浅出MySQL：数据库开发、优化与管理维护》 
2. 《03 | 事务隔离：为什么你改了我还看不见？ 》MySQL实战45讲  丁奇
3. 《33 | MySQL调优之事务：高并发场景下的数据库事务调优》 Java性能调优实战  进入课程  刘超
4. [可能是全网最好的MySQL重要知识点](https://segmentfault.com/a/1190000019619667)  
5. 《07 | 行锁功过：怎么减少行锁对性能的影响？》 MySQL实战45讲  丁奇
6. 《18 | 为什么这些SQL语句逻辑相同性能却差异巨大？》MySQL实战45讲  丁奇
7. [MYSQL死锁的检测与预防](https://www.bilibili.com/video/BV1V3411z7Hj/)

+  {% post_link 'mysqlTransaction' %}  self



