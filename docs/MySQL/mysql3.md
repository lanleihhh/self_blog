

# 事务
## 概述
数据库事务(transcation)是 一次对DB的多条SQL语句执行的管理

是一个**不可分割的工作单位**。事务由 事务开始 与 事务结束 之间执行的**全部数据库操作**组成。

在MySQL中，只有InnoDB引擎支持支持事务功能。


**事务处理**可以用来保护数据库的完整性，保证成批的SQL语句 `要么都执行，要么都不执行`。


事务用来管理 **insert,update,delete** 语句
## 事务特性
 一般事务必须满足4个条件（ACID）
- **原子性**（**A**tomicity，或称不可 分割性）
- **一致性**（**C**onsistency）
- **隔离性**（**I**solation，又称独立性）
- **持久性**（**D**urability） 

**==原子性==**
一个事务中的所有操作，要么都完成，要么都失败，不会结束在中间某个环节。事务在执行过程中发生错误，会**回滚**（rollback）到事务开始前的状态。

**==持久性==**
将数据持久化到硬盘中，不可回滚，即便系统故障也不会丢失。

**==隔离性==**
多个事务并发读取时，保证事物之间互相不影响。
数据库允许多个并发事务同时对数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。**事务隔离**分为不同级别，包括**读未提交**（Read uncommitted），**读已提交**（read committed），**可重复读**（repeatable read），**串行化**（Serializable）。

**==一致性==**
在事务开始之前和结束之后，数据库的完整性没有被破坏。
比如转账，不能A向B转了100，扣了A的钱但没有到B的账户上，结果应该是我们所预期的，==原子性，持久性，隔离性==都是为了保证数据库状态的一致性。

## 事务设置
默认情况下，MySQL启用自动提交模式（变量autocommit 为 on）。也就是只要执行了DML操作的语句，MySQL会立即隐式提交事务。

**autocommit** 分为 **会话**系统变量 与 **全局**系统变量
查询的时候，最好区别是 会话系统变量 还是 全局系统变量。

**MySQL事务处理主要有两种方法：**

**1、用begin,rollback,commit 来实现**
- begin / start transcation 开始事务
- rollback 回滚
- commit 确认提交

**2、用SET改变 MySQL的自动提交模式 （0--不自动   1--自动）**
- SET SESSION / GLOBAL autocommit=0;    禁止自动提交
- SET SESSION / GLOBAL autocommit=1;    开启自动提交

**查看 autocommit 模式**
```mysql
SHOW SESSION / GLOBAL VARIABLES LIKE 'autocommit';
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/e09d4907940f4962a89f22fd78f1dade.png)

## 并发事务处理带来的问题
当两个或多个事务选择同一行，基于最初选定的值进行更新操作时，由于每个事务都不知道其他事务的存在，就会发生**丢失更新**问题，即最后的更新并覆盖了前一个程序员所作的更改。

假设有两个事务A和B ，同时并发。
（1）**脏读**
脏读就是读到了垃圾数据，`A事务`**读取到**`B事务`**未提交的数据**

1. 事务B更新年龄为18，但未提交
2. 事务A读取到数据库中未提交的18
3. 事务B发生了回滚
![在这里插入图片描述](https://img-blog.csdnimg.cn/d87e181119c24e32b90c57eebc8f26f8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

（2）**不可重复读**
在事务A中先后两次读取同一条数据，两次读取的结果不一致，这种现象称为不可重复读。

**脏读与不可重复读的区别**
**脏读**读到的是其他事务未提交的数据。
**不可重复读**读到的是其他事务已提交的数据。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1d3b7955970942e9a9f2ec67c1bfaabc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)


## 隔离级别
**查看隔离级别**
```mysql
SELECT @@global.transaction_isolation,@@transaction_isolation;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d793dfd8ed8e46068ce463065c51db8c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

**设置隔离级别**
```mysql
-- 设置一次会话的隔离级别
SET SESSION TRANSACTION ISOLATION LEVEL 隔离级别

-- 设置全局的隔离级别
SET GLOBAL TRANSACTION ISOLATION LEVEL 隔离级别
```
### 四种隔离级别
- ==读未提交==(read uncommitted)：A事务可以读取到B事务未提交的数据。这会带来==脏读==，==幻读==，==不可重复读==问题
- ==读已提交==(read committed)：A事务只能读取B事务已经提交的修改。 ==避免了脏读==，但是依旧存在**不可重复读**和**幻读**问题。
- ==可重复读==(repeatable read)：**MySQL 默认隔离级别**,同一个事务中多次读取相同的数据返回的结果是一样的。避免了==脏读==和==不可重复读==问题，但是**幻读**依然存在。
- ==串行化==(serializable)：事务串行执行(一次只允许一个事务进行操作),避免了以上所有问题,是**最安全的**,但是**效率最低**


| 事务隔离级别                   | 脏读       | 不可重复读 | 幻读               |
| ------------------------------ | ---------- | ---------- | ------------------ |
| **读未提交(read uncommitted)** | 可能       | 可能       | 可能               |
| **读已提交(read committed)**   | ==不可能== | 可能       | 可能               |
| **可重复读(repeatable read )** | ==不可能== | ==不可能== | ==对InnoDB不可能== |
| **串行化(serializable)**       | ==不可能== | ==不可能== | ==不可能==         |



#### 隔离级别测试
**测试读未提交**

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/5b8dbee7eb5547b9862ee2356bde863d.png)

**测试读已提交**

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED
```

**测试可重复读**

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ
```


## 事务的实现原理
### 两个事务日志
MySQL的InnoDB引擎提供了事务日志：
- **redolog**：重做日志；将数据先保存在日志文件里，定期持久化到磁盘
- **undolog**：回滚日志；操作时，将与操作相反的操作保存在日志中，回滚时，使用日志将数据还原

### 实现原子性
保证一次事务中的若干条SQL语句全部执行或全部不执行
不执行：回滚事务，保存事务开始前的数据，将事务操作后的数据还原到事务开始前的数据

使用undolog，在事务中执行每个操作时，保存一条相反的操作到日志文件中，回滚时借助日志，执行SQL还原到事务开始前的状态

```sql
事务的操作
insert into test(name,age) values('tom',20);
```

```sql
记录到undolog的日志
delete from test where name='tom' and age='20';
```

### 实现持久性
对数据操作时，不会立刻将数据写入磁盘，会先记录到redolog中。
因为每次操作直接写入磁盘，增加了IO次数，效率变低了
数据也不能一直保存在缓存中，如果服务器宕机了，数据就会丢失
InnoDB引擎提供了redolog日志：
可以将数据先保存在日志中，记录哪些数据发生了修改，定期将日志中的数据写入磁盘

### 隔离级别实现
#### MVCC  多版本并发控制
###### 概述
MVCC( Multi-Version Concurrent Control )是MySQL配合 ==Undo log==、==版本链==、==替代锁==，让不同事务的**读--写**、**写--读**操作可以**并发执行**，从而**提升系统性能**。

- `一般在使用读已提交、可重复读	隔离级别的事务中实现。` 
- **写--写**操作互斥，mysql中支持行级锁，不允许多个事务对同一行数据进行操作

######  基本原理
对于使用InnoDB存储引擎的表来说，它的**聚簇索引记录中都包含两个必要的隐藏列**。
- **==trx_id==** :每次对某条聚簇索引记录**进行改动**时，都会把对应的**事务id赋给trx_id隐藏列**
- **==roll_pointer==** :每次对某条聚簇索引记录进行改动时，都会把==旧的版本写入到**undo log**中==，然后这个隐藏列就相当于一个指针，可以通过**roll_pointer**来找到该记录前修改的信息。

######  基本特征
- 每行数据都存在一个版本，每次数据更新时都更新该版本。
- 修改时copy出当前版本，随意修改，各个事务之间无干扰。
- 保存时比较版本号，成功（commit）就覆盖原记录，失败就放弃copy（rollback）。


假设插入该记录的事务 id 为 80，那么此刻该条记录的示意图如下所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/05c5b9ee77b1413c977a5b5a61e97659.png)

假设之后两个 id 分别为 100、200 的事务对这条记录进行 UPDATE 操作，操作流程如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/5d39434f367a44b0b1559b88597aa686.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_18,color_FFFFFF,t_70,g_se,x_16)
每次对记录进行改动,都会记录一条Undo log,每条Undo log 也都有一个roll_pointer属性 (insert操作对应的Undo log没有该属性,因为该记录并没有更早的版本),可以将这些Undo log 都连起来,串成一个链表,情况如下:

![在这里插入图片描述](https://img-blog.csdnimg.cn/becac7a4e58b4184959e48b64916bc10.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
对该记录每次更新后,都会将旧值放到一条Undo log 中,就算是该记录的一个旧版本,随着更新次数增多,**所有的版本被roll_pointer属性连接成一个链表,这个链表就是版本链**。

- 版本链的==头节点就是当前记录最新的值==
- 每个版本中还包含==事务id==，知道了id就知道是哪个事务对该版本进行的操作。

######  ReadView
- 对于使用 `READ UNCOMMITTED` 隔离级别的事务来说，直接读取记录的最新版本就好了
- 对于使用 `SERIALIZABLE`隔离级别的事务来说，使用加锁的方式来访问记录。
- 对于使用 `READ COMMITTED` 和 `REPEATABLE READ`隔离级别的事务来说，==需要用到版本链==。核心问题是：需要判断版本链当中的哪个版本是当前事务可见的。

InnoDB中设计了一个ReadView的概念
- **ReadView**中主要包含当前系统中还有哪些==活跃的读写事务，将它们的事务id放到一个列表（m_ids）中==
- 开启一次会话进行SQL读写时，开始事务时生成ReadView，会把当前系统中正在执行的写事务写入到m_id列表中。

在MySQL中，`READ COMMITTED` 和 `REPEATABLE READ`隔离级别 的区别在于它们生成ReadView的时机不同。
- READ COMMITTED：==每次==读取数据前都生成一个 ReadView，产生不可重复读，其中数据发生改变，版本链也会发生修改，每次读的时候ReadView中的数据就发生改变。
- REPEATABLE READ：在==第一次==读取数据时生成一个 ReadView，之后数据发生改变，版本链发生变化也不影响，之后再读的时候用的是快照中的数据，所以解决了不可重复读。
