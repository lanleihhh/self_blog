
学习Redis之前，需要先了解NoSQL。
>NoSQL:（Not Only SQL)，指**非关系型数据库**。而MySQL、SQL Server等都是关系型数据库。
>由于在磁盘存储。在高并发下访问较慢，大量的查询使得关系型数据库压力过大。


# 关系型数据库与非关系型数据库
**关系型数据库**：
- 是结构化的数据库，支持通用的SQL语句
- 由二维表和二维表之间的联系组成
- 强调ACID规则, 保持数据一致性。

 保证事务安全可靠的四大特性：ACID：
 - Atomicity（原子性）：一个事务中的操作，**要么都成功，要么都失败**，如果发生错误/异常，会回滚到事务开始之前的状态。
 - Consistency（一致性）：事务发生前后，**数据库的完整型**没有破坏，如转账前后，两个账户的金额总和是不变的。
 - Isolation（隔离性）：数据库允许多个事务同时对数据进行**读**、**写**，隔离性要求同一时刻只能有一个事务进行写操作。
 - Durability（持久性）：**事务一旦提交后对数据库的改变是永久的**，其他操作或系统故障都不会造成影响。
  
 **非关系型数据库**：
 - 非关系型，不遵循ACID原则。
 - 易扩展，数据之间没有联系。
 - 性能高，查询快。
 - 数据存储灵活，不用像关系型数据库一样建立表、建立字段，可以存储自定义的数据格式。
 
 # Redis概述
-  **Redis**（Remote Dictionary Server），远程字典服务。
 - 开源，使用C语言编写。
 - 使用==Key-Value==存储数据。
 - 用作数据库，缓存，消息中间件。
 - 支持字符串(strings)，散列(hashes)，列表(list)，集合(sets)，有序集合(sorted sets)。

**Redis的优势**
- 支持数据**持久化**，可以将内存中的数据保存在**磁盘**中。
- 支持多种数据类型：字符串，散列，列表，集合，有序集合。
- 支持数据的**备份**（master-slave 模式的数据备份）。
- 性能高：Redis 能读的速度是 110000 次/s,写的速度是 81000 次/s。
- 原子性：所有操作都是原子性（Redis是**单线程**的）。
- 丰富的特性：支持publish / subscribe ,通知，key过期等特性。

# Linux下安装Redis
## 安装
### 第一种方式：Linux中在线下载
连接服务器，进入要安装源码的目录
1进入要安装的目录
```xml
cd /usr/local/src/

```
2.在线安装

```xml
wget https://download.redis.io/releases/redis-6.2.4.tar.gz
```
### 第二种方式：官网下载，上传到服务器
官网下载: [https://redis.io/download/](https://redis.io/download/)

使用Xftp软件将下载的`.tar.gz`压缩文件上传到linux中

进入要安装源码的目录

解压 压缩包
```xml
tar -zxvf redis-6.0.8.tar.gz
```
进入安装好的源码目录中

```xml
cd redis-6.0.8
```
预编译: 

```xml
make
```

创建安装Redis的目录: 
```xml
mkdir -p /usr/local/redis
```
安装到指定目录

```xml
make install PREFIX=/usr/local/redis
```
进入安装文件 bin目录

```xml
cd /usr/local/redis/bin
```
**启动服务**

```xml
./redis-server
```
## 设置
1.从 redis 的源码目录中复制 `redis.conf` 到 redis 的安装目录

```xml
cp redis.conf /usr/local/redis/bin/
```
2.修改 redis.conf 文件 

```xml
vim redis.conf
```
- 修改为后台运行：`daemonize no` 改为 `daemonize yes`
- 设置密码：`requirepass 密码`
- .注释绑定的 id(127.0.0.1),默认只能本机访问,注释后就可以远程访问了

启动	redis.conf 命令： 

```xml
./redis-server redis.conf
```
查看 Redis 运行状态：

```xml
ps -ef | grep redis
```
进入客户端模式:

```xml
./redis-cli
```
输入密码（在配置文件中修改的`requirepass 密码`）

```xml
auth 密码
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/5f221008d9424142aedceea98336b58c.png)
测试

```xml
ping
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c172a89d237e43eab42d96c5c6e5c136.png)
命令修改密码

```xml
config set requirepass 123456
```
命令查看密码

```xml
config get requirepass
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/f37318ca6acb4a7b803ad02bcc51eb57.png)
退出客户端 `ctrl + c`

