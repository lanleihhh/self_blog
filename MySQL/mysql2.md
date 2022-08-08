
# MySQL架构
## MySQL 的完整架构图
![在这里插入图片描述](https://img-blog.csdnimg.cn/55f4c4fc8f984230b5179d7cf53de9e9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
## 各层介绍
### 连接层
一些**客户端**和**连接服务**,包含本地socket编程和大多数基于client/server工具实现的类似与 **TCP/IP**的通信。

**负责与客户端建立连接、认证、授权** 及相关安全方案。

**连接池**
频繁地 连接/断开 需要消耗大量资源，连接池 将这些连接缓存下来，接收到程序发送连接请求的时候直接使用连接池中建立好的连接，提升服务器的性能。
```java
Authentication：认证
Thread Reuse：线程重用
Connection Limits：连接线程数量限制
Check Memory：检查内存，是指与连接线程相关的内存
Caches：必要时的线程缓存
```

### 服务层

**接收SQL，负责调用函数，存储过程，触发器；对SQL的执行顺序进行排序优化
如果是查询操作，先从缓存中查，缓存中没有再去磁盘中查，提高了性能**

在该层，服务器会解析查询并创建相应的**内部解析树**，并对其完成相应的**优化**,如确定查询表的顺序，是否利用素引等，最后生成相应的执行操作

1. **SQL接口**
	是MySQL的外壳
	一个完整的SQL命令解释器
	**检查SQL语句的语法**
	
3. **解析器**
	SQL命令传递到解析器的时候会被解析器验证和解析
	不检查语法问题
	做语句的执行分析，查询翻译（转为本地可执行的操作）
	
	
4. **查询优化器**
	分析哪种的开销、代价最小
	一旦Optimizer发现有比当前选择的路径更优的实现目的方式，将会改写用户的SQL语句，优化完之后将会交与存储引擎执行
5. **缓存**
	查询缓存，如果查询缓存有命中的查询结果，查询语句就可以直接去查询缓存中取数据。
	`数据库的不断被访问，IO是计算机中最慢的一环，尤其磁盘IO,通常载入到内存中管理.`
	

### 存储引擎层 Pluggable Storage Engine
**真正的执行者，MySQL提供了多种执行引擎，不同引擎特点不同，根据需要来选择。**

存储引擎真正的负责了 MysQL 中数据的存储和提取，服务器 通过 API 与存储引擎进行通信。不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取
### 物理文件存储层
在硬盘上存储系统文件，表数据，各种日志文件。

主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

下图是存储在本机的文件.

![在这里插入图片描述](https://img-blog.csdnimg.cn/148d3a098c0b4177ae5981512aa1c6df.png)



# MySQL引擎
## 概述
MySQL中的数据用各种不用的技术存储在文件中,每种技术都使用了不同的 存储机制、索引技巧、锁定水平,最终提供不同的功能。

这些技术与配套的功能称为存储引擎（也叫表类型）。引擎用于存储、处理、保护数据的核心服务。 **MySQL默认是InnoDB引擎**。
​     
查看支持的引擎
```mysql
SHOW ENGINES; 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/94c8d27c400b4fc4b91e09abe7750ed0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
查看指定表的引擎
```mysql
SHOW TABLE STATUS LIKE '表名'
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/054ba3babd464082b72a8a6017ee4add.png) 

修改引擎
```mysql
方式1
将 mysql.ini 中 default-storage-engine=InnoDB，重启服务
方式2
建表时指定 CREATE TABLE 表名(...)ENGINE=MYISAM;
方式3
建表后修改 ALTER TABLE 表名 ENGINE = INNODB;
```
MySQL中主要的存储引擎（MySQL8）
| 功能         | MYISAM | Memory | InnoDB | Archive |
| ------------ | ------ | ------ | ------ | ------- |
| 存储限制     | 256TB  | RAM    | 64TB   | None    |
| 支持事务     | No     | No     | ==Yes==    | No      |
| 支持全文索引 | ==Yes==    | No     | ==Yes==    | No      |
| 支持数字索引 | ==Yes==    | ==Yes==    |==Yes==    | No      |
| 支持哈希索引 | No     | ==Yes==    | No     | No      |
| 支持数据缓存 | No     | N/A    | ==Yes==    | No      |
| 支持外键     | No     | No     | ==Yes==    | No      |
## MyISAM
- 不支持**事务**
- 不支持**外键**
- 不支持**行级锁**
		 当insert 或 update时，也就是“写操作”时需要锁定整个表，效率会低一些
 - 支持**全文检索（索引）**;存储表的总行数
## InnoDB
是MySQL中的默认存储引擎
InnoDB是**支持事务**的。
支持**行级锁**和**外键**约束
支持**全文索引（检索）**

InnoDB的设计目标是处理大容量数据库系统，MySQL运行时InnoDB会在内存中**建立缓冲池**，用于**缓冲数据和索引**，支持主键自增，不存储表的总行数




| 对比项   | MyISAM                                                 | InnoDB                                                       |
| -------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 主外键   | 不支持                                                 | 支持                                                         |
| 事务     | 不支持                                                 | 支持                                                         |
| 行表锁   | 表锁,即时操作一条记录也会锁住整个表,不适合高并发的操作 | 行锁,操作时只锁某一行,不对其他行有影响,适合高并发的操作      |
| 缓存     | 只缓存索引,不缓存真实数据                              | 不仅缓存索引还要缓存真实数据,对内存的要求较高,而且内存大小对性能有很大的影响 |
| 表空间   | 小                                                     | 大                                                           |
| 关注点   | 性能                                                   | 事务                                                         |
| 默认安装 | Yes                                                    | Yes                                                          |


# 索引
## 为什么使用索引？
真实的业务场景，数据量会非常大，比如有100万条，这100万条数据存储在磁盘中，**一页数据大小为16K**,存储这么多数据需要很多数据页，若查询id='200001'的数据，需要全表扫描，从第一个数据页向后逐页查询。对于大量的数据，逐页查询，性能会急剧下降。因此采用索引。
## 什么是索引？
在Java中有数组索引，字符串索引。都是为了方方便访问里面的数据。

<font color='red'>索引是帮助MySQL 高效获取数据的数据结构。
排好序的快速查找的数据结构。

数据库在存储数据本身之外，还**维护着一个满足特定查找算法的数据结构**。这些数据结构**以某种方式指向数据**，这样就可以在这些数据结构的基础上**实现高级查找算法**，这种数据结构就是**索引**。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1692d693622c463595e4305f468f8a48.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
未来加快Col2的查找，可以维护一个二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找在一定的复杂度内获取到响应数据，从而快速地减速出符合条件的记录。
## 索引的原理
索引类似树的目录，在一本书前加上目录，查找内容时不必逐页翻找，可以快速地定位到所需要的内容页码，借助索引，执行查询时不必扫描整个表救能够快速地找到需要的数据。

通过不断地**缩小**想要获取的数据的**范围**来筛选出最终想要的结果。同时将随机的事件变成顺序的事件，即我们可以总是用同一种查找方式来锁定数据。

## 索引的优势
1. 提高了查询效率(使用B+树存储，有序，将索引数据加载到内存中，**降低数据库的 IO 成本**)

2. 通过索引列对数据进行排序(),**降低数据排序的成本,降低了CPU的消耗**
## 索引的劣势
索引也是一张表,该表保存了主键与索引字段,并指向实体表的记录,所以**索引列也是要占用磁盘空间的**。

虽然索引提高了查询速度，同时却会降低更新表的速度，例如**对表进行insert,update,delete时，MySQL不仅要保存数据，还要保存索引文件**，每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息
## 索引的分类
### 主键索引
设定为主键后数据库会自动建立主键索引 
```mysql
建表后建立主键索引
ALTER TABLE 表名 add PRIMARY KEY 表名(列名); 
删除建主键索引： 
ALTER TABLE 表名 drop PRIMARY KEY ;
```
### 单值索引
- 一个索引只包含单个列，一个表可以有多个单列索引

```mysql
-- 创建单值索引 
-- CREATE INDEX 索引名 ON 表名(列名); 
删除索引： 
DROP INDEX 索引名 ON 表名;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/6a555c257d824f668ddbfb9f17578081.png)

### 唯一索引
索引列的值必须唯一，允许为 null
```mysql
创建唯一索引 
CREATE UNIQUE INDEX 索引名 ON 表名(列名); 
删除索引 
DROP INDEX 索引名 ON 表名;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/084760a5ae5e4efea087e6d8af2f93a4.png)

### 组合（复合）索引
即一个索引包含多个列，**在数据库操作期间，复合索引比单值索引所需要的开销更小**（对于相同的多个列建立索引），当**表的行数远大于索引列的数目时可以使用复合索引**。
```mysql
创建复合索引
CREATE INDEX 索引名 ON 表名(列 1,列 2...); 
删除索引： 
DROP INDEX 索引名 ON 表名;
```

**组合索引最左前缀原则**
表中有 **a,b,c** 3 列,为 a,b 两列创建组合索引,那么在使用时需要满足最左侧索引原则.在使用组合索引的列作为条件时,必须要出现最左侧列为条件,否则组合索引不生效

索引生效
```mysql
SELECT * FROM test WHERE a='' AND b='';
SELECT * FROM test WHERE b='' AND a=''; 
SELECT * FROM test WHERE a='' AND c='';
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d51435fed594406699c1ce3cd40c964f.png)

索引失效
```mysql
SELECT * FROM test WHERE b='' AND c='';
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/36286ac91ee74426a46582aaf44c4251.png)

### 全文索引
like会使索引失效
需要模糊查询时，一般索引无效，这时候就可以使用全文索引了
- 创建索引
 ```mysql
CREATE FULLTEXT INDEX 索引名 ON 表名(字段名) WITH PARSER ngram;
```
- 使用索引
```mysql
SELECT 结构 FROM 表名 WHERE MATCH(列名) AGAINST(搜索词')
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff1578f538e14771b4ee5c19971198d7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

## 索引创建原则
### 需要创建索引的情况
- 主键自动建立唯一索引 **主键索引**
- 频繁作为查询条件的字段(where age=20; )
- 外键关系建立索引
- 排序的字段（排序字段若通过索引去访问将大大提高排序速度）
- 分组的字段

### 不要创建索引的情况
- 表记录太少 (eg:后台管理的一些系统参数表)
- 频繁insert,delete,update的表，更新表时，MySQL需要保存数据和索引文件
- 不作为查询条件的字段
- 数据大量重复的字段（性别，年龄）
`应该只为最经常查询和最经常排序的数据列建立索引，某个列包含许多重复的内容，建立索引没有什么太大效果。`
## 索引的数据结构
mysql索引的数据结构是树，常用的存储引擎**InnoDB采用的是B+树(B+Tree)**。

B+树之前必须先了解**二叉查找树**、**平衡二叉树**（AVLTree）和**平衡多路查找树** （B-Tree），B+树即由这些树逐步优化而来。使其更适合实现外存储索引结构。
### 二叉查找树(BST)
- 一颗二叉树
- 结点的值大于左子树所有节点的值
- 结点的值小于右子树所有节点的值

二叉查找树：https://blog.csdn.net/lanleihhh/article/details/121302715
### 平衡二叉树（AVL-Tree）
平衡二叉树是二叉排序树的基础上，对树的深度进行了限制，从而减少了查找比较的次数
- 是一棵二叉查找树
- 左子树与右子树的深度差在-1、0、1内，否则对子树进行旋转调整。
### 平衡多路二叉树（B+Tree）
相对于平衡二叉树，对父结点的直接子结点个数，不再仅限于2，

可以指定m(自定义)，这样可以在树的深度不大量增加的前提下，保存更多的结点。
- 排好序的,一个节点可以存储多个数据 （横向扩展 使得树的高度降低）
- 非叶子节点不存储数据,只存储索引,可以放更多的索引
- 数据记录都存放在叶子节点中.   找到了索引,也就找了数据
- 所有叶子节点之间都有一个链指针。（适合区间查询  age>20   age<50）
注意：`所有结点均带有值`
![在这里插入图片描述](https://img-blog.csdnimg.cn/006185ae2b0447a6bd2221fd3028ebca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
使用B+树来存储索引，B+树会自动排序
- B+树的每个节点可以存储多个数据(横向扩展)，树的高度降低了
- 非叶子节点存储索引，叶子节点存储数据
- 每个叶子节点之间有指针指向，便于区间范围查询
## 聚簇索引和非聚簇索引
### 聚簇索引
找到了索引就找到了需要的数据，那么这个索引就是聚簇索引，所以 **主键** 就是聚簇索引。

键可以直接找到数据

学号是索引，根据学号只查询学号，这种场景是聚簇索引。
### 非聚簇索引
**索引**的存储和**数据**的存储是**分离**的，也就是说找到了索引但没找到数据，**需要根据索引上的值（主键）再次回表查询**，非聚簇索引也叫**辅助索引**

学号是索引，根据学号查询姓名，虽然学号加了索引，但是还是要查询姓名。根据学号找到主键，通过主键回表查询姓名，这种场景是非聚簇的。

**举例说明两种索引的场景**

```mysql
CREATE TABLE student (
	id BIGINT, 
	NO VARCHAR (20), 
	NAME VARCHAR (20), 
	PRIMARY KEY (id), 
	UNIQUE KEY idx_no (NO ) 
)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9d5f2fc2ffce454d8552b51fc097b280.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

**第一种**
直接根据**主键查询**获取所有字段数据，此时主键是**聚簇索引**，因为主键对应的索引叶子节点存储了 id=1 的所有字段的值
>SELECT * FROM student WHERE id = 1

**第二种**
根据 编号 查询`编号`和`名称`，编号本身是一个唯一索引，但查询的列包含了学生编号和学生名称，当命中编号索引时，该索引的节点的数据存储的是主键 ID，需要根据主键 ID 重新查询一次，所以这种查询下 `NO` 是**非聚簇索引**
>SELECT NO,NAME FROM student WHERE NO = 123

**第三种**
我们根据编号查询编号(有人会问知道编号了还要查询？要，你**可能需要验证该编号在数据库中是否存在**)，这种查询命中编号索引时，直接返回编号，因为所需要的数据就是该索引，不需要回表查询，这种场景下 `NO` 是**聚簇索引**。

总结:
- MySQL 中 InnoDB 引擎的索引和文件是存放在一起的,找到索引就可以找到数据,是聚簇 式设计.
	![在这里插入图片描述](https://img-blog.csdnimg.cn/29bfb683cadb4bf087f76f742dabe62a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
- 而 MyISAM 引擎采用的是非聚簇式设计,索引文件和数据文件不在同一个文件中
	![在这里插入图片描述](https://img-blog.csdnimg.cn/0b0e3bc0d2fd4bf89c49d976b0b8f057.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8a38f2ac34cb49598098f9d3c1353f1b.png)
- test.MYD，存储数据；
- test.MYI，存储索引；
