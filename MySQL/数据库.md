
# Mysql数据库

## 1. 数据库是什么

 数据库（DataBase）为了方便数据的存储和管理，它将数据按照特定的 规则存储在磁盘上,就是一个存储数据的仓库。通过数据库管理系统，可 以有效的组织和管理存储在数据库中的数据 

1. 为了方便数据**存储和管理**,将数据存储到电脑硬盘

2. 通过DBMS(数据库管理系统)**管理**数据

### 1.1数据库的相关概念

 DB：**数据库**（DataBase） 存储数据的容器，它保存了一系列有组织的数据。

 DBMS：**数据库管理系统**（DataBase Management System） 又称为数据库软件或数据库产品，用于创建或管理DB。

 SQL：**结构化查询语言**（Structure Query Language） 是一种特殊 目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以 及查询、更新和管理关系数据库系统 

### 1.2. 为什么学习数据库

实现运行时 数据持久化到本地

实现方便数据管理

### 1.3 数据库分类

关系型数据库(RDBMS Relation DataBase Management System  )

Oracle (Oracle)

**MySQL (Oracle)**

SQL Server(微软)

DB2  (IBM)

非关系型数据库

Mongdo DB

Redis

## 2.  Mysql

脚本语言,不需要编译,直接通过某种解释器解释执行

### 2.1 语法规范

- 不区分大小写
-  语句以分号"**;**"结尾
- 关键字大写
- 表名/列名小写

### 2.2 注释

**单行注释**: 

注释文字

 注释文字(注意-- 与注释文字之间有空格)

**多行注释**: /* 注释文字*/

## 3. DDL 

DDL 数据定义语言(Data Definition Language)  是 用于**创建**和**修改**数据库==表==结构的语言

常用语句:

- create 创建
- drop    删除
- alter    修改
- rename 重命名 

注意:mysql不能修改数据库名称,只能修改表名称

###  3.1数据库存储数据的特点 

● 将数据放到**表**中，表再放到**库**中

● 一个数据库中可以有**多个表**，每个表都有一个名字，用来标识自己。表名具有 **唯一**性。 

● 表具有一些特性，这些特性定义了数据在表中如何存储，类似Java中“类”的 设计。 

● 表由**列**组成，我们**也称**列为**字段**。所有表都是由一个或多个列组成的，每一列 类似java中的”属性” 

● 表中的数据是按行存储的，每一行类似于Java中的“对象” 



### 3.2 表概述

 在关系数据库中，数据库表是一系列二维数组的集合，用来代表和储存数据对象之间的关系。它由纵向的列和横向的行组成

表是数据库基本存储单位

记录: 一行信息

字段: (列),表示存储何种信息



### 3.4 设计数据库表

存储数据之前,要先创建数据库,再创建表

```mysql
/*
	创建数据库
CREATE DATABASE [IF NOT EXISTS] 数据库名 [CHARSET 字符集名]
	
   删除数据库
   DROP DATABASE 数据库名;
   DROP DATABASE  IF EXISTS 数据库名;
   
   修改数据库字符集
   ALTER DATABASE 数据库名 CHARSET 字符集名;
*/
```

```mysql
-- 不存在就创建数据库
CREATE DATABASE IF NOT EXISTS school_db; 
-- 不存在就创建指定字符集的数据库
CREATE DATABASE IF NOT EXISTS sqlname3 CHARSET utf8;

-- 将要执行的语句全部选中,点击执行查询(F9  SQLyog)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/009b3738b5c540bf8cd591e6353e0f79.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center)


```mysql
-- 修改数据库字符集
ALTER DATABASE school_db CHARSET gbk;
-- 删除数据库
DROP DATABASE sqlname3;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/347f6e9b1ad24c668645e715bb50d925.jpg#pic_center)






在给表添加元素时,我们先了解一下mysql中的数据类型

- char(n)            长度为n的定长字符串           (如n=4,即时存储一个字符,也占4个长度)
- varchar(n)       长度为0~n的长度可变字符串
- date                 日期   年月日
- datetime          时间   年月日,时分秒

**浮点型**

float(M,D)        4字节

double(M,D)    8字节

M:数据总长度

D:小数点后长度

eg . 10.15 >> (4,2)

**整型**

TINYINT: 1字节

SMALLINT: 2字节

MEDIUMINT: 3字节

INT:				4字节

BIGINT:			8字节



BLOB :大文本,存储图片,新闻



数据库已经创建好,现在创建表

```mysql
/*
	创建表
	CREATE TABLE 表名(列名 数据类型 [约束] [默认值] [ 注释]...)
*/
-- 创建基础表
CREATE TABLE student(
	-- 学号
	xuehao INT,
	-- 姓名	
	NAME VARCHAR(4),
	-- 性别
	sex CHAR(1),
	-- 生日
	birthday DATE,
	-- 身高(175.5)
	height FLOAT(4,1),
	-- 电话()
	phone INT(11),
	-- 登记时间
	register_time DATETIME
)
```

点击执行,运行无错误时,得到一个表文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/5dbfacf74f94456696dc8267d8634346.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/3f9cedba60ec4a27b57a93ad1047a02a.jpg#pic_center)


```mysql
-- 删除表
DROP TABLE student;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/4c6eb55e30524320ab30dfea9a256ad5.jpg#pic_center)


可以看到表student从数据库school_db里已经删除

```mysql
-- 修改表名
RENAME TABLE student TO student1;
RENAME TABLE student1 TO student;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/8709ea5442844bc789cd28586c9e176f.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




```java
-- 复制表结构
CREATE TABLE t_student1 LIKE student;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c60ed0ec753a46c59648766fa6b47c21.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




PRIMARY KEY AUTO_INCREMENT主键自增
	约束
PRIMARY KEY 主键约束

主键约束 不能为空,不能重复

check()   检查约束 

NOT NULL  不为空约束

UNIQUE    唯一性

DEFAULT '默认值'

字段注释 comment'注释'



```mysql
#修改表结构
-- 添加主键约束
ALTER TABLE t_student1 ADD PRIMARY KEY(xuehao);
-- 删除主键约束(删除不写主键列名,表中只有一个主键)
ALTER TABLE t_student1 DROP PRIMARY KEY;

-- 设置主键自增
ALTER TABLE t_student1 MODIFY xuehao INT AUTO_INCREMENT;
-- 删除主键自增
ALTER TABLE t_student1 MODIFY xuehao INT;

-- 设置不能为空or可以为空
ALTER TABLE t_student1 MODIFY NAME VARCHAR NOT NULL;
ALTER TABLE t_student1 MODIFY sex CHAR NOT NULL;

-- 添加唯一约束
ALTER TABLE t_student1 ADD CONSTRAINT unique_phone UNIQUE(phone);
-- 删除唯一约束
ALTER TABLE t_student1 DROP INDEX unique_phone;

-- 添加/删除检查约束
ALTER TABLE t_student1 ADD CONSTRAINT check_height CHECK(height>100);
ALTER TABLE t_student1 DROP CHECK  check_height;
```

为phone添加唯一约束后,输入相同的字符串,会报错

![在这里插入图片描述](https://img-blog.csdnimg.cn/3cc9f8cfab274a7786864298f2c6a79a.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


检查约束: CHECK(height>100),只有输入数据大于100,才不会报错

![在这里插入图片描述](https://img-blog.csdnimg.cn/54821e830f5a43f1b12df2232d778155.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


```mysql
-- 添加列
ALTER TABLE t_student ADD weight FLOAT(4,1) FIRST; -- 添加到最前面
ALTER TABLE t_student ADD weight FLOAT(4,1) AFTER height; -- 向指定元素后加 
ALTER TABLE t_student ADD weight FLOAT(4,1); -- 向末尾加

-- 删除列
ALTER TABLE t_student DROP weight;

-- 修改列名
ALTER TABLE t_student CHANGE phone mobile_phone VARCHAR(11);

-- 修改列的数据类型
ALTER TABLE t_student MODIFY xuehao VARCHAR(10);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/8b1c2e09fc184f609b4ce7c502e35283.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




## 4. DML  

DML数据操作语言 （Data Manipulation Languag )

insert插入 delete 删除 update 修改 

now() : 获取数据库所在系统的当前时间

```mysql
-- 插入方式1
INSERT INTO t_student1(xuehao,NAME,sex,birthday,height,phone,register_time)
		VALUES(1001,'张一','男','2010-1-1',185.3,'1234567891',NOW());
		    
-- 插入方式2
INSERT INTO t_student1(xuehao,NAME,sex,birthday,height,phone,register_time)
		VALUES(1002,'张三','男','2001-1-1',175.3,'1234567892',NOW()),
		      (1003,'李四','女','2002-1-1',165.3,'1234567893',NOW());

-- 插入方式3
INSERT INTO t_student1 SET xuehao=1004,NAME='王五',sex='男',birthday='1999-1-1',
height=165.2,phone='12345678902',register_time=NOW();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/845202d23cc149d9a099ade4c31e449c.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


```mysql
-- 复制表结构
CREATE TABLE stdi LIKE t_student1;

-- 复制表数据
INSERT INTO stdi(xuehao,NAME,sex,birthday,height,phone,register_time)
		SELECT xuehao,NAME,sex,birthday,height,phone,register_time FROM t_student1

INSERT INTO stdi SET xuehao=1005,NAME='赵六',sex='男',birthday='1999-5-9',
height=178.2,phone='1234567894',register_time=NOW();


-- 修改语句
UPDATE stdi SET NAME='Tom',sex='女' WHERE xuehao = 1001 -- 修改时要注意条件

-- 删除语句
DELETE FROM stdi WHERE xuehao = 0;
DELETE FROM stdi WHERE xuehao = 1;
-- 清空整张表
TRUNCATE TABLE stdi;
```

与上一张被复制的表比较,新增了一行,删除了学号为0,1的两行,将学号1001的信息改变

![在这里插入图片描述](https://img-blog.csdnimg.cn/cace573d7e304068b77a986203b9e487.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 5. DQL

### 5.1DQL

（Data Query Language） 数据查询语言

基础查询,使用频率最高的操作	

语法:

select 查询列表(结果) from 表名

select 结果 form 表名 where 条件 排序 分组 行数限制 子查询

```mysql
-- 查询常量
SELECT 1001 FROM stdi;
SELECT 10*10 FROM stdi;
SELECT height-5 FROM stdi;

-- 查询版本号
SELECT VERSION();
-- 查询name列的长度
SELECT CHAR_LENGTH(NAME) FROM stdi;

-- 查询所有列
SELECT * FROM stdi;

-- 查询特定列
SELECT NAME FROM stdi;

-- 去除重复数据
SELECT DISTINCT * FROM stdi;
-- 去除指定数据的重复数据
SELECT DISTINCT sex FROM stdi;
```

​														查询软件版本

![在这里插入图片描述](https://img-blog.csdnimg.cn/9d4fffd4d1cc45aabde0a950b715aadf.jpg#pic_center)




​			查询姓名列的长度

![在这里插入图片描述](https://img-blog.csdnimg.cn/3cbbae06795b42fab653462f03672e4d.jpg#pic_center)


​						查询所有列

![在这里插入图片描述](https://img-blog.csdnimg.cn/d4c4ea445670442e9756e39f8b802567.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


​															查询姓名列

![在这里插入图片描述](https://img-blog.csdnimg.cn/0893f3767c7f40979dff855331fd4e19.jpg#pic_center)


​												去除重复数据

![在这里插入图片描述](https://img-blog.csdnimg.cn/06825852bd2b42a5bcb7b49cfc628171.jpg#pic_center)






- 单行函数--字符函数

```mysql
length()：获取参数值的字节个数
char_length()获取参数值的字符个数
concat(str1,str2,.....)：拼接字符串
upper()/lower()：将字符串变成大写/小写
substring(str,pos,length)：截取字符串 位置从1开始
instr(str,指定字符)：返回子串第一次出现的索引，如果找不到返回0
trim(str)：去掉字符串前后的空格或子串,trim(指定子串 from 字符串)
lpad(str,length,填充字符)：用指定的字符实现左填充将str填充为指定长度
rpad(str,length,填充字符)：用指定的字符实现右填充将str填充为指定长度
replace(str,old,new)：替换，替换所有的子串
```

```mysql
/*
	使用函数对查询的结果进行处理
	函数: 
	单行函数 :对查询的每行数据进行操作
	分组函数(聚合函数): 多行转为一行
	
*/

-- 查询字节数     汉字3字节,英文字符一字节
SELECT LENGTH(NAME) FROM stdi;

-- 查询字符数     汉字=字符=1
SELECT CHAR_LENGTH(NAME) FROM stdi;

-- 拼接字符串   as 设置别名
SELECT CONCAT(NAME,':',sex,':',height) FROM stdi;
SELECT CONCAT(NAME,':',sex,':',height) NAME  FROM stdi;
SELECT CONCAT(NAME,':',sex,':',height) AS NAME  FROM stdi;


-- 大写upper小写lower
SELECT UPPER(NAME) FROM stdi;
SELECT LOWER(NAME) FROM stdi;


-- 截取字符串  substring(列名,开始位置(1),截取长度)
SELECT SUBSTRING(NAME,1,1) FROM stdi;

-- instr(str,子串) 返回子串第一次出现的索引，如果找不到返回0  类似java中的indexOf()
SELECT INSTR(NAME,'1') FROM stdi

-- trim 去掉字符串前后空格或子串
-- trim(列名)  trim(子串 from 列名)
SELECT xuehao CHAR_LENGTH(TRIM (NAME)) FROM stdi;
SELECT xuehao TRIM ('4' FROM NAME) FROM stdi;

-- lpad(列名,指定长度,填充的子串) 左填充 rpad 右填充
SELECT LPAD(NAME,4,'0') FROM stdi;
SELECT RPAD(NAME,4,'~') FROM stdi;

-- replace(列,old,new) 替换所有子串
SELECT REPLACE(sex,'男','女') FROM stdi;
```

- 单行函数--逻辑函数:

 case when 条件 then 结果1 else 结果2 end; 可以有多个when 

if null 

if (条件,结果1,结果2)

以上表为例

```mysql
SELECT 
	NAME,
	(CASE WHEN YEAR(birthday)>1990 AND YEAR(birthday)<2000 THEN '90后' 
		WHEN YEAR(birthday)>2000 AND YEAR(birthday)<2010 THEN '00后'
		ELSE '10后'
		END)年龄段
FROM stdi;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/3523cb18128141ab935cee12e4cfc61a.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_12,color_FFFFFF,t_70,g_se,x_16#pic_center)


- 单行函数--数学函数

```mysql
round(数值)：四舍五入
ceil(数值)：向上取整，返回>=该参数的最小整数
floor(数值)：向下取整，返回<=该参数的最大整数
truncate(数值,保留小数的位数)：截断，小数点后截断到几位
mod(被除数,除数)：取余，被除数为正，则为正；被除数为负，则为负
rand()：获取随机数，返回0-1之间的小数
```

```mysql
-- 四舍五入
SELECT ROUND(height) FROM stdi;
-- 向上取整 ，返回>=该参数的最小整数
SELECT CEIL(5.5) FROM stdi;
-- 向下取整 , 返回<=该参数的最大整数
SELECT FLOOR(5.5) FROM stdi;
-- 截断(小数点后截断几位)
SELECT TRUNCATE(5.1234,2) FROM stdi;
-- 取余，被除数为正，则为正；被除数为负，则为负
SELECT MOD(5,2) FROM stdi;
-- 获取随机数，返回0-1之间的小数
SELECT RAND() FROM stdi;
```

- 单行函数--日期函数

```mysql
/*
	日期函数
*/
-- now 当前日期加时间
SELECT NOW()
SELECT CURDATE()
SELECT CURTIME()
-- 年月日时分秒,必须有列名参数
SELECT YEAR(birthday) FROM stdi;
SELECT MONTH(birthday) FROM stdi;
SELECT DAY(birthday) FROM stdi;
SELECT HOUR(register_time) FROM stdi;
SELECT MINUTE(register_time) FROM stdi;
SELECT SECOND(register_time) FROM stdi;

-- str_to_date(str,格式)字符串转为指定格式的日期
SELECT STR_TO_DATE('2021-7-6','%Y-%m-%d')

-- date_format(列,格式)格式化
SELECT DATE_FORMAT(birthday,'%Y-%m')FROM stdi;`t_student1`
SELECT COUNT(*),DATE_FORMAT(birthday,'%Y-%m')birth FROM stdi GROUP BY birth -- count分组

-- datediff(big,small) 计算两个时间相差天数
SELECT DATEDIFF(CURDATE(),birthday) FROM stdi-- 计算从出生到现在的天数
```



日期格式

| 格式 | 含义                        |
| ---- | --------------------------- |
| %Y   | 年,四位                     |
| %m   | 月，数值(00-12)             |
| %d   | 月的天，数值(00-31)         |
| %H   | 小时 (00-23                 |
| %i   | 分钟，数值(00-59)           |
| %s   | 秒(00-59)                   |
| %f   | 微秒                        |
| %T   | 时间, 24-小时 (hh:mm:ss)    |
| %j   | 年的天 (001-366             |
| %w   | 周的天 （0=星期日,6=星期六) |



分组函数

```mysql
/*
	分组函数
	sum(数值类型的列)  	求和
	avg(数值类型的列)   	求平均值
	max(列)		   	求最大值
	min(列)		    	求最小值
	count(* / 主键/ 指定列)	    计数
	和分组函数一同查询的字段要求是group by 后的字段
*/

-- 全部求和
SELECT SUM(height) FROM stdi;

-- 分组求和
SELECT SUM(height),sex FROM stdi GROUP BY sex;

-- 平均值
SELECT AVG(height) FROM stdi;

-- 最大值
SELECT MAX(height) FROM stdi;
-- 最小值
SELECT MIN(height) FROM stdi;

-- 统计数量
SELECT COUNT(*) FROM stdi;
```

### 5.2 基础查询

```mysql
/*
	条件查询
	语法:  select 结果 from 表名 where 条件
	比较:
	等于= 
	不等于!=,<>
	其他> < >= <=
	
*/

#               比较
-- 等于
SELECT NAME FROM stdi WHERE sex='男' 

-- 不等于
SELECT NAME FROM stdi WHERE sex!='男' 
SELECT NAME FROM stdi WHERE sex<>'女' 

-- > < >= <=
SELECT NAME FROM stdi WHERE height>170
SELECT NAME FROM stdi WHERE height<170
SELECT NAME FROM stdi WHERE weight>=100
SELECT NAME FROM stdi WHERE weight<=100


#             逻辑:  and(与) or(或) not(非) 

--  and   and可以连接多个条件,多个条件必须同时成立
SELECT NAME FROM stdi WHERE sex='男' AND weight<100

-- or 
SELECT NAME FROM stdi WHERE sex='男' AND weight>100 OR sex='女'
SELECT NAME FROM stdi WHERE height=185.3 OR height=165.3

-- not 
SELECT NAME FROM stdi WHERE  NOT weight=100 
```

模糊查询

```mysql
/*
	模糊查询
	LIKE ：是否匹配于一个模式 一般和通配符搭配使用，可以判断字符型数值或数值型
	通配符:%任意多个字符,_任意单个字符
	in   判断某字段是否属于in列表的某一列
	is null   为空
	is not null  不为空
	between A and B   A~B之间
	
*/			
-- is null    is not null
SELECT NAME FROM stdi WHERE weight IS NULL 
SELECT NAME FROM stdi WHERE weight IS NOT NULL 

-- in(1,2,3) 是1 or 2 or 3    not in(1,2,3) 不是1 and 2 and 3
SELECT NAME FROM stdi WHERE height IN(185.3,165.3)
SELECT * FROM stdi WHERE height NOT IN(185.3,165.3)

-- between A and B 两者之间
SELECT NAME FROM stdi WHERE height BETWEEN 170 AND 180

-- 通配符 % 任意    _ 一个        
SELECT NAME FROM stdi WHERE NAME LIKE '张%'
SELECT NAME FROM stdi WHERE NAME LIKE '_T__'
```

union  & union all

```mysql
#             union 和  union all

-- union 将多条查询结果合并,去掉重复行
SELECT NAME FROM stdi WHERE sex='男'
	UNION
SELECT NAME FROM stdi WHERE height>170

-- union all 不会去掉重复数据
SELECT NAME FROM stdi WHERE sex='男'
	UNION ALL
SELECT NAME FROM stdi WHERE height>170
```

排序 order by

```mysql
/*
	排序
	order by 子句 [desc/asc]
	desc 降序
	acs  升序
	不写默认升序
	
	子句可以是单个或多个字段,表达式,函数,别名
	
*/
-- 默认升序
SELECT * FROM stdi ORDER BY birthday;
-- 有条件时,排序在条件之后,对筛选后的结果排序
SELECT * FROM stdi WHERE sex='男' ORDER BY birthday DESC;
-- 多个字段进行排序,第一个相同时,使用第二个字段排序
SELECT * FROM stdi ORDER BY CHAR_LENGTH(NAME),height;
```

limit  限制数量

```mysql
/*
	数量限制
	放在sql语句末尾
	limit(开始位置,查询数量)
*/
SELECT * FROM stdi WHERE sex='男' ORDER BY birthday DESC LIMIT 0,2
```

分组统计



```mysql
/*
	分组统计
	语法:
    select 分组函数，列（要求出现在group by的后面）
    from 表
    [where 筛选条件]
    group by 分组的列表
    [having 分组后的筛选]
    [order by 子句]
*/
-- 查询男生女生人数
SELECT sex,COUNT(*) FROM stdi GROUP BY sex;
SELECT sex,SUM(height) FROM stdi GROUP BY sex;
SELECT sex,AVG(height) FROM stdi GROUP BY sex;

-- 查询男女生人数哪个多
 SELECT
  sex,
  COUNT (*)
FROM
  stdi
WHERE height > 100 	-- 对原始表数据进行条件筛选
GROUP BY sex 		-- 按条件分组
HAVING COUNT (*) > 3	-- 对分组后的数据进行条件筛选
ORDER BY COUNT (*) DESC -- 对处理完的数据排序
LIMIT 0, 2		-- 限制查询结果的显示行数
```

子查询

 含义：出现在其他语句中的select语句，称为子查询或内查询；外部的查询语句，称为主查询或 外查询. 

分类： 按子查询出现的位置： 

- select后面：仅仅支持标量子查询 

- from后面：支持表子查询 

- where或having后面：支持标量子查询，列子查询，行子查询 

按功能、结果集的行列数不同： 

- 标量子查询（结果集只有一行一列）
- 列子查询（结果集只有一列多行） 
- 行子查询（结果集有一行多列）（较少）
-  表子查询（结果集一般为多行多列） 

```mysql
 -- 在修改语句中使用子查询,子查询不能是当前正在操作的表
# UPDATE stdi SET NAME='匿名' WHERE xuehao=(SELECT xuehao FROM stdi WHERE height=180)
# DELETE  FROM stdi WHERE NAME=(SELECT NAME FROM stdi WHERE sex='女' AND height=169.6)

 -- 重点 在查询语句中使用子查询
 -- 在一条SQL语句中出现2个以上的表名时,可以为表名定义别名
 -- 在select后面写子查询  用外面查询
 
 SELECT st.name ,
	(SELECT s.name FROM stdi s WHERE s.name = st.name)
 FROM stdi st;
 
 -- where 后面的子查询
 -- 标量子查询
 SELECT * FROM stdi WHERE height=(SELECT MAX(height) FROM stdi) 
 
 -- 列子查询  一列多行
 SELECT * FROM stdi WHERE height IN (SELECT height FROM stdi WHERE height>=170)
 
 -- 行子查询 一行多列
 SELECT * FROM stdi WHERE (height,weight)=(SELECT MAX(height),MAX(weight) FROM stdi)
 
 -- from后面 使用表子查询 返回多行多列
 -- 查询男女人数哪个多
 
 SELECT * FROM
	(SELECT sex,COUNT(*)c FROM stdi GROUP BY sex)t
	WHERE t.c>3
 
 INSERT INTO stdi(NAME,sex,score)
		VALUES('Dim','男',50)
```



## 6. 多表关联

### 关联的目的

- 数据库设计范式
- 建立冗余较小,结构合理的数据库,设计时必须遵循一定的规则(这种规则在关系型数据库中称为范式,范式是符合某一种设计要求的总结)

- 六种范式:

  - 第一范式(1NF)

  - 第二范式(2NF)
  - 第三范式(3NF)
  - 巴斯-科德范式(BCNF)
  - 第四范式(4NF)
  - 第五范式(5NF)

- 第一范式是最基本范式,在其基础上满足更多规定要求则为第二范式,其余范式以此类推

- 一般数据库满足第三范式就OK了

### 数据库设计范式

**第一范式(确保每列原子性)**

第一范式是关系型数据库最基本的范式,若表中字段都是不可分解的原子值,则该数据库满足第一范式

也就是每列只能有一个属性值,若下表中第一行的dept_desc的值包含了开发软件和销售软件两个值,则不满足第一范式

![在这里插入图片描述](https://img-blog.csdnimg.cn/9817c81dbe79497baa79e4209e424021.jpg#pic_center)




如图表,每列都是不能再分的原子值,满足第一范式

**第二范式(有主键,其他字段依赖主键)**

主键的作用:

- 设置主键,每一组(行)数据就有了唯一性,通过主键直接定位到所需要的这组数据

- 主键与其他字段组成的一组数据表示同一个东西,主键是唯一的,它们依赖主键,也就成了唯一的

第二范式就是在第一范式的基础上使其他属性都依赖于主键

```mysql
-- 添加主键               
-- 方式1   创建表时添加列:  列名 数据类型 PRIMARY KEY
-- 方式2   创建表后:  ALTER TABLE 表名 ADD PRIMARY KEY(列名)
```

**第三范式**

消除传递依赖,方便理解,看作"**消除冗余**"



非第三范式:

![在这里插入图片描述](https://img-blog.csdnimg.cn/ba24838550a64a94832c72b38a932146.jpg#pic_center)


第三范式:

![在这里插入图片描述](https://img-blog.csdnimg.cn/f342dfac05a0486e92214399e81d9092.jpg#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/e867745032db4c8baca8873be44a6f9c.jpg#pic_center)






### 外键

```mysql
-- 弱关联   人为定义的关系 没有实际的约束  删除关联数据没有任何限制
ALTER TABLE stdi ADD grade_id INT AFTER NAME;

-- 删除 grade_id
 ALTER TABLE stdi DROP grade_id 
```

外键:引用另一条表的某条记录

添加外键约束

```mysql
-- 添加外键约束
-- ALTER TABLE 表名 ADD [CONSTRAINT 约束名] FOREIGN KEY(外键列) REFERENCES 关联表(主键);
-- 删除外键
-- ALTER TABLE 表名 DROP FOREIGN KEY
```

数据库中的表与表之间的关联关系靠主键(primary key)与外键(foreign key)建立

```mysql
-- 添加外键约束
-- ALTER TABLE 表名 ADD [CONSTRAINT 约束名] FOREIGN KEY(外键列) REFERENCES 关联表(主键);
   -- 强关联        添加外键约束,      外键关联另一个表的主键     与t_grade中的主键id关联
-- 添加外键约束
ALTER TABLE employee ADD CONSTRAINT fk_id FOREIGN KEY(dept_id) REFERENCES department(id)
```

如图,employee表的dept_id与department表的id关联了起来

![在这里插入图片描述](https://img-blog.csdnimg.cn/7e54b7ea60124b58875ae264c5bed5b7.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




- 主表中没有对应的记录时,不能将记录添加到从表
- 不能更改主表中的值而导致从表中的记录孤立 
- 从表存在与主表对应的记录，不能从主表中删除该行 
-  删除主表前，先删从表  

### 关联查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/ea29ce5aa0d54aea990bbab206ccf525.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


 也叫多表查询，当查询的字段来自于多个表时，就会用到连接查询 

创建一个学生表

```mysql
-- 创建表
CREATE TABLE stdi (
	xuehao INT,
    NAME varchar(10)
)

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/98f25702a8154783a271bc162ed616c5.jpg#pic_center)


创建一个课程表

```mysql 
-- 创建课程表
       CREATE TABLE t_course (
        id INT PRIMARY KEY AUTO_INCREMENT,
        SUBJECT VARCHAR (10)
      )
      
      -- 添加值
      INSERT INTO t_course (id, SUBJECT)
      VALUES
        (1, 'java'),
        (2, 'C#'),
        (3, 'Python'),
        (4, 'Go'),
        (5, '操作系统') 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6ac5ddfeff82487d8c8a436eac5cb10c.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


建立一个表,关联学生表与课程表

```mysql
-- 学生选课表  (学生课程关联表)
    CREATE TABLE stdi_course (
    stdi_xuehao VARCHAR (10),
    course_id INT,
    CONSTRAINT fk_sid FOREIGN KEY (stdi_xuehao) REFERENCES stdi (xuehao),
    CONSTRAINT fk_cid FOREIGN KEY (course_id) REFERENCES t_course (id)
) 
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/92d3aad45e154f7eae84758771ffd9fd.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


#### 笛卡尔乘积现象 

![在这里插入图片描述](https://img-blog.csdnimg.cn/2d433172159f434187e9e70c0e1af774.jpg#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/2725f4f50543498aa7c39eda0781416a.jpg#pic_center)




笛卡尔乘积---表1有m行,表2有n行,查询结果有m*n行

```mysql
-- 笛卡尔乘积现象   表1有m行，表2有n行，结果=m*n
SELECT
*
FROM
stdi,
t_grade 
```

执行此查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/c5ce591dc62b4081b620150455b2f2e5.jpg#pic_center)


**发生原因**:

没有有效的连接条件

**解决方法**:

添加有效的连接条件

```mysql
-- 添加条件   多表时为表定义别名,通过别名调用表中的列
SELECT
*	
FROM
stdi s,
t_grade g                       -- 先合并表,后筛选
WHERE s.grade_id=g.id 
```

执行此查询:

![在这里插入图片描述](https://img-blog.csdnimg.cn/bd98a0c55be049c0ab2e95b93ba853be.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


#### 内连接(INNER JOIN)

 把满足了条件的两张表中的交集数据查询出来 

```mysql
-- 语法
Select 
结果 
from 表1,表2 
where 表1.column1 = 表2.column2
```



##### 等值连接

```mysql
-- 内关联 
-- 等值连接  作用与上面相同,内关联效率高
SELECT 
* 
FROM
stdi s INNER JOIN t_grade g 
ON s.grade_id = g.id 
```



##### 非等值连接

```mysql
-- 向学生表中添加分数列
ALTER TABLE stdi ADD score INT AFTER NAME


-- 分数等级表
CREATE TABLE score_level(
level_name VARCHAR(5),
lower_score INT,
upper_score INT
)
```

查询

```mysql
-- 查询 学生分数等级
SELECT 
s.xuehao,
s.name,
s.score,
sl.level_name
FROM stdi s INNER JOIN score_level sl
ON s.score BETWEEN sl.lower_score AND sl.upper_score
```

执行此查询:

![在这里插入图片描述](https://img-blog.csdnimg.cn/c65b72fc01a44f39a49ce00d18565433.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_15,color_FFFFFF,t_70,g_se,x_16#pic_center)


##### 自连接

```java
 -- 省市县(区)
 CREATE TABLE t_area(
 id INT PRIMARY KEY COMMENT'子id',
 NAME VARCHAR(10),
 pid INT COMMENT'父id'
 )
```

手动添加值

![在这里插入图片描述](https://img-blog.csdnimg.cn/b3c6a731d8884ef183437ba3e6b8c9c3.jpg#pic_center)


```mysql
-- 查询
SELECT 
ta.name,tp.name pname 
FROM t_area ta INNER JOIN t_area tp
ON ta.pid = tp.id
WHERE ta.pid=2
```

执行此查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/e47ed72baf364925ace577a5a2b6eae4.jpg#pic_center)


#### 外连接

##### 左外连接 (LEFT JOIN)

```mysql
-- 语法
SELECT 结果 FROM
表1 LEFT JOIN 表2 ON
表1.column1 = 表2.column2
```

查询

```mysql
-- 左外关联
-- 无论关联条件是否成立,都会将左边表的数据全部查询出来(成绩单,无论有无成绩,名字学号必须显示)
SELECT 
s.name,
s.xuehao,
g.grade
FROM stdi s
LEFT JOIN t_grade g
ON s.grade_id=g.id;
```

执行

![在这里插入图片描述](https://img-blog.csdnimg.cn/0e8bdded513848e2b196829f53289e20.jpg#pic_center)


##### 右外连接(RIGHT JOIN)

```mysql
-- 语法
SELECT 结果 FROM
表1 RIGHT JOIN 表2 ON
表1.column1 = 表2.column2
```

查询

```mysql
-- 右外关联
-- 统计每个年级的人数
SELECT 
COUNT(*),
g.grade
FROM stdi s 
RIGHT JOIN t_grade g
ON s.grade_id = g.id
GROUP BY g.id
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/91e269efd2b14a2eacb1812eaeadb226.jpg#pic_center)

