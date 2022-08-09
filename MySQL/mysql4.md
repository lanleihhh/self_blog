
# 锁机制
与Java中的锁类似，A事务获取到锁之后才可以对表中的数据进行读取或写入操作，其他事务不能访问被锁的信息，只能等待A事务回滚or提交来结束事务从而释放锁。

事务特性中的**隔离性**：同一时刻只能有一个事务来访问数据，InnoDB引擎的锁机制可以保证隔离性。

MyISAM支持表锁，InnoDB支持表锁、行锁

## 行锁
- 只对操作的一行or多行加锁（更新数据时使用）
- 并发性最高(容易冲突，可能会死锁)，开销大(频繁加锁释放锁)
- 使用了索引，根据索引查找指定的行
- 分为共享锁和独占锁
## 表锁
- 对操作的这张表加锁
- 实现简单，开销小，并发性不高
- 全表扫描，不使用索引
- 分为共享锁和独占锁
## 间隙锁
- 对一个区间加锁，介于行锁与表锁之间
- 使用范围条件时，会给符合条件的数据的索引加锁

<font color='red'>
1. 没有加索引的列作为查询条件时，会锁住整张表
</font>

<font color='red'>
2. 当执行条件为区间范围时，不管范围内有没有这条数据，都会被锁定，其他事务无法操作
</font>
<font color='red'>
3. 执行insert、delete、update等操作时，会自动对这些记录加锁
</font>

## 意向锁
- 意向共享锁：对整个表加共享锁之前，需要先获取到它的意向共享锁
- 意向排它锁：对整个表加共享锁之前，需要先获取到它的意向共享锁

意向锁不会和数据行共享锁/排它锁发生冲突，只是给表设置的一个标识，记录这个表现在有没有行锁，其他事务读到标识就知道自己能不能加表锁了，不用逐行地去判断有没有行锁，提高了加锁表的效率。


## 共享锁、独占锁
- 共享锁：读锁，A事务可以和B事务同时读取数据
	主要为了支持并发读的场景，读时不允许写操作。
- 独占锁：写锁，A事务对一条记录修改时，其他事务不能修改这条记录
	主要为了在事务进行写操作时，不允许其他事务修改。
### 在操作之前对表加锁
```java
-- 给test表加写锁---------独占锁
LOCK TABLE test WRITE;

-- 给test表加读锁---------共享锁
LOCK TABLE test READ;

-- 释放锁
UNLOCK TABLE
```
### 在执行select查询的时候加锁
==InnoDB中select默认不加锁==
#### for update 排它锁
<font color='red'>
1. 在一个select查询语句后加上 for update：变成独占锁<br>
2. 其他事务在此期间都不能读取修改该记录，知道记录释放锁<br>
3. 其他事务不能对持有排它锁的记录加排它锁or共享锁
</font>

可以查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b3bf12182a147b6904854f94c7721b2.png)
不能修改
![在这里插入图片描述](https://img-blog.csdnimg.cn/5680d8c6a108422dadbd04bfec46bfea.png)
其他事务不能再加锁
![在这里插入图片描述](https://img-blog.csdnimg.cn/f616d30900724563990bfe7d2b0ed1d2.png)

#### lock in share mode 共享锁
<font color='red'>
1. 在一个select查询语句后加上 lock in share mode：变成共享锁<br>
2. 其他事务在此期间只能对该记录进行查询操作，等记录释放锁后，可以修改<br>
3. 其他事务可以对已经持有锁的字段加共享锁，不能加独占锁
</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/26df52c47e764a46bbef608781ea2068.png)

`[共享锁] 和 [排他锁] 均阻塞不了 【快照读】`

# SQL优化
## 优化的建议
查询是真实项目中频率最高的，查询效率不高，并发量大时，影响整体的性能
1. **避免整张表扫描**
	 对作为where条件的列 或 order by 排序的列<font color='blue'>建立索引</font>
2. **避免索引失效**，索引失效会使用整张表进行扫描
 	1. 对where后条件进行<font color='blue'>null值判断</font>，为null索引会失效
 	
 	2. 避免使用 <font color='blue'>  != , <, > </font>  这些操作符
 	
 	3. 避免在where后使用	<font color='blue'>or</font> 作为连接条件

    4. 避免使用  <font color='blue'>IN , NOT IN</font>。对于简单的数据可以用between来代替

    5. 避免使用 <font color='blue'>LIKE</font>，可以使用<font color='blue'>全文索引</font>来实现模糊查询

    1. 避免在where后对字段进行<font color='blue'>表达式操作</font>

    2. 避免在where后对字段<font color='blue'>使用函数</font>


  3. 建立适当的索引，索引虽然加快了select的效率，但降低了insert和update的效率(这两操作会重建索引)
  4. 尽量使用数值型字段，(查询时：字符会一个一个比较，数值比较一次就好了)
      比如一些状态，可以使用0,1表示，在java中来对状态进行处理

  5. 使用varchar代替char，varchar省空间，用多少，分配多少，char定长直接分配固定空间，可能浪费

  6. 不使用`select * from table`，需要什么返回什么：`select name,age from student`
  7. 避免返回大量数据
  8. 避免字段为NULL
       `空值''`不占空间，NULL占空间
       设置了可以为NULL的列column,查询中有column，索引失效
       count()统计的数据如果是column，NULL值会被忽略，但是空值`''`可以统计到

## EXPLAIN 执行计划

在一条查询SQL语句之前添加 explain，会返回执行计划的信息（不会执行这条语句）
`insert、update、delete之前也可以使用explain来打印信息，但一般并不关注`
![在这里插入图片描述](https://img-blog.csdnimg.cn/d2bfcdefb1fc465eb5236f2a8b61ec10.png)
EXPLAIN可以打印一条SQL的信息
- 表的读取顺序
- SQL查询语句的类型
- 可能用到哪些索引，哪些索引又被实际使用
- 表与表之间的引用关系
- 一个表中有多少行被优化器查询 .....

![在这里插入图片描述](https://img-blog.csdnimg.cn/5c23da7abd7544f1933556129d4ecae6.png)
### 1.id

SELECT识别符。这是SELECT的查询序列号

SQL执行的顺序的标识，SQL从大到小的执行

1. id相同时，执行顺序由上至下

2. 如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行

3. id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行

### 2.select_type
>(1) SIMPLE(简单SELECT，不使用UNION或子查询等)
(2) PRIMARY(子查询中最外层查询，查询中若包含任何复杂的子部分，最外层的select被标记为PRIMARY)
(3) UNION(UNION中的第二个或后面的SELECT语句)
(4) DEPENDENT UNION(UNION中的第二个或后面的SELECT语句，取决于外面的查询)
(5) UNION RESULT(UNION的结果，union语句中第二个select开始后面所有select)
(6) SUBQUERY(子查询中的第一个SELECT，结果不依赖于外部查询)
(7) DEPENDENT SUBQUERY(子查询中的第一个SELECT，依赖于外部查询)
(8) DERIVED(派生表的SELECT, FROM子句的子查询)
(9) UNCACHEABLE SUBQUERY(一个子查询的结果不能被缓存，必须重新评估外链接的第一行)



### 3.table
显示这一步所访问数据库中表名称（显示这一行的数据是关于哪张表的），有时不是真实的表名字，可能是简称，例如上面的e，d，也可能是第几步执行的结果的简称

### 4.type
对表访问方式，表示MySQL在表中找到所需行的方式，又称“访问类型”。

常用的类型有： ALL、index、range、 ref、eq_ref、const、system、NULL（从左到右，性能从差到好）
>**ALL**：Full Table Scan， MySQL将遍历全表以找到匹配的行
**index**: Full Index Scan，index与ALL区别为index类型只遍历索引树
**range**:只检索给定范围的行，使用一个索引来选择行
**ref**: 表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值
**eq_ref**: 类似ref，区别就在使用的索引是唯一索引，对于每个索引键值，表中只有一条记录匹配，简单来说，就是多表连接中使用primary key或者 unique key作为关联条件
**const**、**system**: 当MySQL对查询某部分进行优化，并转换为一个常量时，使用这些类型访问。如将主键置于where列表中，MySQL就能将该查询转换为一个常量，system是const类型的特例，当查询的表只有一行的情况下，使用system
**NULL**: MySQL在优化过程中分解语句，执行时甚至不用访问表或索引，例如从一个索引列里选取最小值可以通过单独索引查找完成。



### 5.possible_keys
**指出MySQL能使用哪个索引在表中找到记录，查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询使用（该查询可以利用的索引，如果没有任何索引显示 null）**
>该列完全独立于EXPLAIN输出所示的表的次序。这意味着在possible_keys中的某些键实际上不能按生成的表次序使用。
如果该列是NULL，则没有相关的索引。在这种情况下，可以通过检查WHERE子句看是否它引用某些列或适合索引的列来提高你的查询性能。如果是这样，创造一个适当的索引并且再次用EXPLAIN检查查询


### 6.Key
**显示MySQL实际决定使用的键（索引），必然包含在possible_keys中**
>如果没有选择索引，键是NULL。要想强制MySQL使用或忽视possible_keys列中的索引，在查询中使用FORCE INDEX、USE INDEX或者IGNORE INDEX。

### 7.key_len
**表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度（key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的）**

不损失精确性的情况下，长度越短越好 

### 8.ref
列与索引的比较，表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值

### 9.rows
估算出结果集行数，表示MySQL根据表统计信息及索引选用情况，估算的找到所需的记录所需要读取的行数

### 10.Extra
额外的信息说明 
**Using filesort**: 当 Query 中包含 ORDER BY 操作，而且无法利用索引完成排序操 作的时候,Mysql 无法利用索引完成排序的操作称为”文件排序”. 

**Using temporary**: 使了用临时表保存中间结果，MySQL 在对查询结果排序时 使用临时表。常见于排序 order by 和分组查询 group by。 

**Using index** 表示相应的 select 操作中使用了索引，避免访问了表的数据行，效率不错！ 如果同时出现 using where，表明索引被用来执行索引键值的查找；如果没 有同时出现 using where，表明索引用来读取数据而非执行查找动作。 

**Using where** 表示使用到了索引 , 但是也进行了 where 过滤
