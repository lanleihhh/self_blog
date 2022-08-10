
# 主从复制
将Redis **主机**(master) 中的数据，复制到其他 **从机**(slave) 中。

使用一个Redis的实例作为主机，其他实例作为从机。客户端向主机中写入数据，主机将数据复制到所有从机中，读取数据时由从机来返回数据，达到**读写分离**。
- 客户端发送写命令给主机
- 主机将数据同步到从机中
- 客户端发送读命令给从机

## 使用redis集群的好处？
- 数据访问量很大时，如果只有一个redis服务器，当发生故障时，所有的请求到达了数据库，数据库压力过大可能导致宕机。
- 实现读写分离。主机负责写入数据，同步数据；从机负责查询数据

# 哨兵机制
哨兵是一个独立的进程。哨兵通过给每台redis服务器发送命令，等待服务器响应与否，从而达到监控各个服务器的运行情况。

## 主机宕机后哨兵机制做什么？
若主机master宕机了，会通过选举策略从各个从机中选出一个作为主机，将其他从机与选出的主机连接。
1. 将宕机的主机下线
2. 从slave中选举出一个作为master
3. 通知所有的slave连接新的master
4. 启动新的master和slave
5. 复制数据

## 宕机的主机恢复后哨兵机制做什么？
当原来的主机恢复后，将选举出的主机变回从机，所有的从机重新连接恢复的主机。

# 配置主从复制
主机的配置

```yaml
注释bind 127.0.0.1                #任何 ip 都可以访问 
daemonize yes                    #后台运行 
pidfile /var/run/redis_6379.pid  #进程号文件 
logfile "6379.log"     			 #日志文件  修改文件名只是为了区分 
dbfilename dump_6379.rdb          #数据文件
requirepass 123                  #主机密码
```
从机配置
从机①
```yaml
注释bind 127.0.0.1                #任何 ip 都可以访问 
daemonize yes                    #后台运行 
pidfile /var/run/redis_6380.pid  #进程号文件 
logfile "6380.log"     			 #日志文件  修改文件名只是为了区分 
dbfilename dump_6380.rdb          #数据文件
requirepass 123                  #从机密码
replicaof 主机ip(122.112.196.221) 主机端口(6379) 
masterauth 主机密码(123)
```
从机②
```yaml
注释bind 127.0.0.1                #任何 ip 都可以访问 
daemonize yes                    #后台运行 
pidfile /var/run/redis_6381.pid  #进程号文件 
logfile "6381.log"     			 #日志文件  修改文件名只是为了区分 
dbfilename dump_6381.rdb          #数据文件
requirepass 123                  #从机密码
replicaof 122.112.196.221 6379
masterauth 123
```
开启不同端口的服务

```xml
./redis-server redis_6379.conf
./redis-server redis_6380.conf
./redis-server redis_6381.conf
```
查看几个服务的运行情况
![在这里插入图片描述](https://img-blog.csdnimg.cn/720062d2797c4f2f91ccc39bd9e92b70.png)

根据指定线程端口进入客户端
```xml
./redis-cli -p 6379
./redis-cli -p 6380
./redis-cli -p 6381
```
使用命令`info replication`查看redis服务器的信息

6379主机
![在这里插入图片描述](https://img-blog.csdnimg.cn/cbd8ced8772642cc9d1c5f13b260b596.png)
6380从机

![在这里插入图片描述](https://img-blog.csdnimg.cn/8395c93dc87a42aebde10bb7d9738108.png)

6381从机

![在这里插入图片描述](https://img-blog.csdnimg.cn/b18463f972a14dc69bec45b4cd6b5b5c.png)

配置成功，来测试一下

**主机set数据**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2a6b9f7e221c460787e6abf68cdbbb4d.png)

**两个从机get数据**

![在这里插入图片描述](https://img-blog.csdnimg.cn/79e1cc92d0ad43c586c34b2749d8cf62.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/901d47fea0834ac7a50f2d22dc0aa309.png)

关闭主机的进程，从机依然能获取数据，因为数据已经同步到从机了。
此时主机恢复连接，从机也可以获取主机新写入的数据。









