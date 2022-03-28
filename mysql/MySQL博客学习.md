# 什么是事务

指的数据库一系列数据操作，要么全部成功要么全部失败，失败后就会回滚所有的操作。
MySQL原生的MyISAM引擎就不支持事务，这是InnoDB取代MyISAM的重要原因之一。

## 四大特性

原子性(Atomicity):事务开始后所有操作，要么全部做完，要么全部不做，不可能停滞在中间环节。**事务执行过程中出错，会回滚到事务开始前的状态，所有的操作就像没有发生一样**。也就是说事务是一个不可分割的整体，就像化学中学过的院子，是物质构成的基本单位。
一致性(Consitency):事务开始前和结束后，**数据库的完整性约束没有被破坏**。比如A向B转账,A转了钱，但是B却没有收到，这显然是不合理的。
隔离性(Isolation):同一时间，**只允许一个事务请求同一个数据**，不同的事务之间彼此没有任何干扰。比如A账户向B账户转账这个事务，同一时刻，不允许C账户向B账户转账
持久性(Durability):**事务完成后**，事务对数据库的所有更新都将被保存到数据库，不能回滚。所谓的完成之后，就是我们已经提交了这个事务。

## 隔离级别

### 读未提交(read-uncommit):事务A能读取到事务B的未提交的操作。

### 读已提交(read-committed):一个事务提交之后，他做的变更才会被其他事务看到。

### 可重复读(repeatable read):一个事务执行过程中看到的数据，总是跟这个事务在启动的时候看到的数据是一致的。当然在可重复读隔离级别下，未提交变更对其他事务也是不可见的。

### 串行化(serializable):对同一行记录，也就是我们数据库中的一条记录，"写"会加"写锁","读"会加"读锁"。当出现读写锁冲突的时候，后访问的事务必须等前一个事务执行完成，才能继续执行。所以这种隔离级别下所有的数据是最稳定的，但是性能也是最差的，像极了多线程。

## 解决并发的问题

### 脏读:事务A读取了事务B更新的数据，然后B回滚擦操作，那么A读取到的数据就是脏数据。

### 不可重复读:事务A多次读取同一数据，事务B在事务A多次读取的过程中，对数据作了更新，然后事务A再次读取数据的时候和之前读取的数据不一致。

### 幻读:系统管理员A将数据库中所有的成绩具体分数改为ABCDE等级，但是系统管理员B插入了一条数据为具体的分数，当系统管理员修改结束后发现还有一条数据没有修改过来，好像幻觉和一样。

不可重复读和幻读的区别就是不可重复读是针对数据修改来说的，而幻读则是针对数据的新增。解决不可重复读的问题只需要锁住满足条件的行，解决幻读的话就需要用到锁。
只有串行化的隔离级别解决了全部这3个问题，其他的3个隔离级别都有缺陷。
![选区_158.png](https://i.loli.net/2021/04/15/YPG1BJ3iczaSCfy.png)

## 案例

```pgsql
CREATE TABLE `student`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `age` int(11) NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 66 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;
```

![选区_159.png](https://i.loli.net/2021/04/15/dA6VGafBYhrHUEI.png)
事务A查询id=2的学生的age,一个事务B更新id=2的学生的age。在4钟隔离级别x1、x2和x3的值不一致。
![选区_160.png](https://i.loli.net/2021/04/15/FJWX4E3TgmOyQB2.png)

### 读未提交:

x1的值是23(如果事务B又回滚了x1的值就是脏的)。x2、x3的值也是也是23。

### 读已提交:

x1的值是22，因为B改了，但是A看不到。(如果B后面回滚了，x1的值不变，解决了脏读)，x2、x3的值是23

### 可重复读:

整个A事务开启之后，x1、x2的值都是一样的。(只要A还没提交，解决了不可重复读)，而x3的值是23，因为A提交了，能看到B修改的值了

### 串行化:

B在执行更改期间会被锁住，直至A事务提交。(A在读的期间，B不能写。得以保证此时数据是最新的，解决了幻读)所以x1、x2都是22，而最后的x3在B提交之后执行，它的值就是23。就跟多线程访问对象一样一样的，某个线程获得对象锁之后，其他对象就无法进入同步代码块，从而实现了多线程的同步。

## 数据隔离级别的实现

实际上，数据库里面会创建一个视图，访问的时候以视图逻辑结果为准。**在"可重复读"的级别下，视图是一开始就创建的**，因为创建了之后，后面的查询结果就都是基于这张动态的表(视图)来的，所以不用担心会读到其他数据。在"读已提交"隔离级别下，**这个视图是在每条SQL语句执行之后创建的**，也就是说，每次执行SQL语句都会创建一个新的视图，所以就会将最新的数据查询到。"**读未提交"直接返回记录上的最新值，没有视图概念**；而"串行化"隔离级别下直接用加锁的方式来避免并行访问。

### 设置数据隔离级别

oracle默认隔离级别是读提交,而MySQL是可重复读。**当数据库进行迁移的时候，请把级别设置成与搬迁之前的一致。**
查看事务隔离级别
![选区_161.png](https://i.loli.net/2021/04/15/ALKxH76i1sgaBZm.png)
自己动手进行设置
语句格式:set [作用域] transaction isolation level [事务隔离级别]
其中作用域可选:session(会话)、global(全局);隔离级别就是上面提到的4钟，不区分大小写。
设置全局隔离级别为读提交

```pgsql
set global transaction isolation level read committed;
```

## 事务的启动

1.显式启动

```applescript
**start** transaction
update student set name='张三 where id=2
**commit**;
```

使用rollback进行回滚
2.set autocommit=0,这个命令会将线程的自动提交关掉。意味着如果你执行一个select语句，这个事务即启动了，而且不会自动提交。
3.set autocommit=1,表示MySQL自动开启和提交事务。比如执行一个update语句，语句只完成后就自动提交了。
4.start transaction with consistent snapshot;上面提到的begin/start transaction命令并不是一个事务的起点，在执行到它们之后的第一个操作InnoDB表的语句，事务才真正的启动。如果你想要马上启动一个事务，可以使用start transaction with consistent snapshot命令。第一种启动方式，一致性视图是在执行第一个快照读语句时创建的；第二种启动方式，一致性视图是在执行start transaction with consistent snapshot时创建的。

# 事务隔离级别的实现

## 什么是MVCC？

MVCC,全称 Multi-Version Concurrency Control，即多版本并发控制。MVCC是一种并发控制的方法，一般在数据库管理系统中，实现对数据库的并发访问，在编程语言中实现事务内存。
MVCC使得数据库**读对数据不会加锁，也就是说，select查询不会加锁，提高了数据版本的并发控制能力；数据库写才会加锁**。借助MVCC，数据库可以实现**READ COMMITTED，REPEATABLE READ**等隔离级别，用户可以查看当前数据的前一个或者前几个历史版本，保证了ACID中的I特性(隔离性)。
MVCC只在REPEATABLE READ和READ COMMITED等两个级别下工作。其他隔离级别都和MVCC不兼容，**因为READ UNCOMMITED总是读取最新的数据行，而不是符合当前事务版本的数据行**。而SERIALIZABLE则会**对所有行都加锁**。

### InnoDB中的MVCC

InnoDB中每个事务都有一个**唯一的事务ID**，记为transaction_id。**它在事务开始时向InnDB申请，按照时间先后严格递增。**
其实每行数据都有多个数据版本，这就依赖undo log来实现了。每次事务更新数据会生成一个新的数据版本，同时会生成一个transaction_id作为row trx_id，另外还会有一个回滚指针来指向undo log中的上一个版本。
所以，innoDB中的MVCC是通过每行记录后面的两个隐藏的列来实现的。一列是事务ID:trx_id;另一列是回滚指针:roll_pt。

## undo log－回滚日志

保存了事务发生之前的数据的一个版本，可以用于回滚，同时可提供多版本并发控制下的读(MVCC)，也即非锁定读。
根据操作的不同，分为insert undo log和update undo log。

### insert into undo log

insert操作产生的undo log，因为**insert操作记录没有历史版本只对当前事务本身可见，对于其它事务此记录不可见，**,什么意思呢，在此之前没有insert过记录，不会有历史版本，所以insert undo log 可以在事务提交后直接删除而不需要进行purge操作。
**purge**的主要任务是**将数据库中已经mark del的数据删除**,另外也会**批量回收undo pages**
所以，插入数据时。它的初始状态是这样的:
![选区_166.png](https://i.loli.net/2021/04/17/cmikp6XwRUE2rye.png)
**update undo log**
UPDATE和DELETE操作产生的undo log都属于同一类型:**update_undo。**(update可以视为insert 新数据到原位置，delete旧数据，undo log暂时保存旧数据)
**事务提交到history list上，没有事务要用到这些回滚日志，即系统中没有比这个回滚日志更早的版本，purge线程将进行最后的删除操作**
一个事务修改当前数据:
![选区_167.png](https://i.loli.net/2021/04/17/v8UpZTCBzJn1SeV.png)
另一个事务修改数据：
![选区_168.png](https://i.loli.net/2021/04/17/q385fVacYEzSpwb.png)
这样的同一条记录在数据库中存在多个版本，就是上面提到的多版本并发控制MVCC。
另外，借助undo log通过回滚可以回到上一个版本状态。比如要回到V1版本只需要顺序执行两次回滚即可。

## read-view

read-view是InnDB是在**实现MVCC时用到的一致性视图**，用于支持RC(读提交)以及RR(可重复读)隔离级别的实现。
read-view不是真实存在的，只是一个概念，undo log才是它的体现。它主要是通过版本和undo log计算出来的。作用是**事务能看到哪些数据。**
**每个事务或者语句有自己的一致性视图。普通查询语句是一致性读，一致性读会根据row trx_id 和一致性视图确定视图确定数据版本的可见性。**

read view中主要包含当前系统中还有哪些活跃的事务，在实现上InnDB为每个事务创造了一个数组，用来保存这个事务启动瞬间，当前正在**活跃(还未提交)的事务**
事务ID随时间严格递增,**把系统中已提交的事务ID的最大值记为数组的低水位,以创建过的事务ID+1记为高水位**

**这个视图数组和高水位就组成了当前事务的一致性视图**(read view)
![选区_170.png](https://i.loli.net/2021/04/22/bG7O8Sg19PJMncu.png)
规则如下:
1.如果trx_id在灰色区域，表明被访问版本的trx_id小于数组中低水位的id值，也即生成该版本的事务在生成read view 前已经提交，所以该版本可见，可以被事务访问。
2.如果trx_id在橙色区域，表明访问版本的trx_id大于数组中高水位的id值，也即生成了该版本的事务在生成read view才生成，所以该版本不可见，不能被当前事务访问。
3.如果在绿色区域，就会有两种情况：
a>trx_id在数组中，证明这个版本是由还未提交的事务生成的，不可见
b>trx_id不在数组中，证明这个版本是由已提交的事务生成的，可见

参考博客：[https://mp.weixin.qq.com/s/l6...](https://mp.weixin.qq.com/s/l62CAZ55ZU9f9fsLOQR71A)