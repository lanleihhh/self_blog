
# 线程模型
## 为什么Redis是单线程？
Redis内部使用**文件事件处理器**(`file event handler`)，这个处理器是单线程的，因此Reids被叫做单线程模型。

- 在6.x之前：处理客户端的连接、请求、读写操作都是单线程执行的。
- 在6.x之后：处理**客户端的连接、请求是多线程的**，**读写操作是单线程**的，保证了Redis的线程安全


## 为什么在Redis6.x引入多线程
Redis的性能瓶颈在于**内存**和==网络IO==，网络IO的读写在Redis执行期间占用了大部分CPU的时间，内存可以扩展，**网络IO的性能提升是Redis性能提升的关键**，因此采用多线程做网络请求处理。

## 为什么Redis作为单线程模型效率也很高？
- **在内存中操作**，读取速度快
- **数据结构简单**，以键值对存储，底层使用哈希结构，查询效率为O(1)
- **非阻塞的IO多路复用**，多路复用监控多个socket连接客户端，使用一个线程处理多种情况，避免了IO阻塞
- **避免了线程切换**带来的开销，是线程安全的



# 持久化
- Redis中的**数据存储在内存中，容易丢失**，因此需要持久化（保存到硬盘）的方式保存数据。
- Redis提供了**RDB**（生成快照）和**AOF**（记录每次对数据的操作）两种持久化机制,两种方式都可以在`redis.conf`中设置
- Redis**默认使用RDB**方式
## RDB（Reids Database）
在**指定时间间隔**内将内存中的数据(Key-Value)生成**快照**存储到硬盘中。
默认文件：`dump.rdb`![在这里插入图片描述](https://img-blog.csdnimg.cn/1381187ea2b3463da68231f73b552423.png)
### RDB的优点、缺点
**优点**：
- 适合大规模的数据恢复(存储在文件中，redis开机会还原到内存)
- 对数据的完整性要求不高

**缺点**：
- 需要一定的时间间隔触发，若遇到进程退出、服务器宕机等情况，最后一次修改的数据会丢失。
###  触发快照的条件
#### 1.redis.conf中save 满足配置 save [seconds]  [changes]
- save m秒 n个键改变
 `save <seconds> <changes>`

![在这里插入图片描述](https://img-blog.csdnimg.cn/b48253ad3d644fbc87c346f8de1eea94.png)

==如果不需要持久化，可以注释所有save行来停用该功能==。
#### 2. 执行 flushall 命令
先set两个键值对，执行`flushall`
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2931f0b36bf949debf9ca3df23b7f5f6.png)
#### 3. 退出Redis

设置键值对后退出Redis
![在这里插入图片描述](https://img-blog.csdnimg.cn/1e987e4bc6c9430dab33b42ccd53a489.png)
dump.rdb文件：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a851053cfd874737864a30c5755ea76a.png)

- 备份会自动生成一个 `dump.rdb`
- 下次启动Redis，会将文件中的数据还原到内存中

## AOF（Append Only File）
- 以日志形式记录redis执行过的==写==命令。
- 文件中只能追加，不能修改
- redis启动后，会将日志中的命令从前到后执行一遍来恢复上次的数据。

**开启AOF持久化机制**
1.打开redis.conf文件


```yaml
appendonly no-->appendonly yes
appendfilename "appendonly.aof"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/3145e550bd944f4a81d6fdb326e45e7f.png)
2.AOF同步机制
![在这里插入图片描述](https://img-blog.csdnimg.cn/0e44a4aded804d5087a3ec0393a8d503.png)
3.重启Redis生效




# 事务
- Redis事务就是一组**命令的集合**，一个事务中的所有命令都会被序列化
- **不保证原子性**——一个事务中的几个命令，只有发生错误的命令不执行，其他命令执行。（数据库原子性是事务中所有语句都执行或都不执行）
- Redis事务中没有隔离级别，**可以隔离其他客户端**，将一个事务中的命令执行完后，再让其他客户端中的命令执行，**不会发生交替执行**
- 事务的命令不会直接去执行，执行`exec`（execute）命令时才执行事务中的命令

redis 的事务
```xml
开启事务（multi） 
命令入队（.....） 
执行事务（exec） 
放弃事务(discard)
```

