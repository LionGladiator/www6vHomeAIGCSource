---
title: Mysql事务和锁
date: 2020-08-14 17:22:20
tags:
  - mysql
categories:  
  - mysql
---

<p></p>
<!-- more -->

## 一. MyISAM 和 InnoDB

  描述  | MyISAM  |  InnoDB 
  :-: | :-: | :-: 
  行锁(并发高，会死锁)| × | √ (默认支持)<br>Record lock: 锁记录<br>Gap lock: 锁范围，不锁记录<br>Next-key lock： 锁范围+锁记录
  表锁(并发低，不会死锁)| √ |  √
  事务和崩溃恢复| × |  √
  外键| × |  √
  MVCC| × |  √ <br> 在READ COMMITTED 和 REPEATABLE READ时有效 


## 二. 事务隔离级别

隔离级别| 脏读|  不可重复读<br>（重点是修改）| 幻影读<br>（重点是新增或者删除）
:-: | :-: | :-: | :-:
READ-UNCOMMITTED|  √| √| √
READ-COMMITTED|  ×| √| √
REPEATABLE-READ<br>（**InnoDB默认支持**）| ×| ×| √
SERIALIZABLE|  ×| ×| ×

> innodb对于行的查询使用next-key lock
  **Next-locking keying、Gap锁为了解决Phantom Problem幻读问题**
  当查询的索引含有唯一属性时(单条记录)，将next-key lock降级为record key


{% asset_img  mvcc.JPG  MVCC（一致性读视图） %}
  

## 三. 锁
1. **行锁， 锁优化**
   在InnoDB事务中，**行锁**是在需要的时候才加上的，但并不是不需要了就立刻释放，而是要等到事务结束时才释放。这个就是**两阶段锁协议**。
   知道了这个设定，对我们使用事务有什么帮助呢？那就是，**如果你的事务中需要锁多个行，要把最可能造成锁冲突、最可能影响并发度的锁尽量往后放.**

2. 死锁和死锁检测
当出现死锁以后，有两种策略：
+ 一种策略是，直接进入等待，直到超时。这个超时时间可以通过参数
innodb_lock_wait_timeout来设置。
innodb_lock_wait_timeout的默认值是50s。 实际中不用这种策略。

+ 另一种策略是，发起**死锁检测**，发现死锁后，主动回滚死锁链条中的某一个事务，让其他事
务得以继续执行。将参数innodb_deadlock_detect设置为on，表示开启这个逻辑。
带来的问题：每个新来的被堵住的线程，都要判断会不会由于自己的加入导致了死锁，这是一个时间复杂度是
O(n)的操作。假设有1000个并发线程要同时更新同一行，那么死锁检测操作就是100万这个量级
的。虽然最终检测的结果是没有死锁，但是这期间要**消耗大量的CPU资源**。
一种解决思路是**控制并发度**：并发控制要做在数据库服务端。如果有中间件，可以考虑在中间件实现；如果
团队有能修改MySQL源码的人，也可以做在MySQL里面。基本思路就是，**对于相同行的更新，
在进入引擎之前排队**。这样在InnoDB内部就不会有大量的死锁检测工作了。
另一种解决思路是**在应用层上优化**:你可以考虑通过将一行改成逻辑上的多行来减少锁冲突。 比如，一个账户1条记录变10条记录。

3. 隐式锁和显示锁
显示锁
SELECT ... LOCK IN SHARE MODE(加共享锁);
SELECT ... FOR UPDATE(加排他锁);

## 参考:
1. 《深入浅出MySQL：数据库开发、优化与管理维护》 
4. [Mysql事务总结](../../../../2015/02/21/transaction/) self
6. [可能是全网最好的MySQL重要知识点](https://mp.weixin.qq.com/s/M1dLLuePpdM9vA3F1uJGyw)  
12. 《MySQL实战45讲 - 行锁功过：怎么减少行锁对性能的影响？》  丁奇
13. 《MySQL实战45讲 - 为什么这些SQL语句逻辑相同性能却差异巨大？》  丁奇
2. 《MySQL实战45讲 - 03 | 事务隔离：为什么你改了我还看不见？ 》  丁奇




