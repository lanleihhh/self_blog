# 设置key失效
像缓存、验证码等临时数据，不必一直存在，若想要在一定时间内销毁它，可以使用Redis提供的对key设置过期时间的命令来做。
Redis中默认有效时间是-1，也就是永久有效。

## 1.设置值时就规定有效时间
- `ex` 以秒为单位
- `px` 以毫秒为单位

```xml
set key value ex 20 设置20s有效期
ttl key             查看key剩余时间(s)
pttl key            查看key剩余时间(ms)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6220fa5fdcda4051a90b514f7865edbe.png)
设置了20秒的有效时间，第二次再访问已经没有数据了
## 2.在已经有值时，给值规定有效时间
```xml
expire key        时间(s) 
pexpire key       时间(ms)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/064bb6ceaf224f659e391b6b19eb5b7a.png)

# key的过期策略
>Redis 使用 ==惰性删除 + 定期删除==
>redis 数据库由`dict`和`expires`两个字典组成
>- dict 负责存储所有的键值对
>- expires 负责存储键的过期时间
>
**Redis中可以对键设置过期时间，key过期后，Redis是如何处理的？**
- redis提供了三种方式：立即删除、惰性删除、定时删除


## 1.立即删除
给key设置过期时间时，创建回调函数，当key有效期结束后立即执行回调函数，删除键释放内存。

缺点：**删除操作会占用cpu时间，影响redis性能**。


## 2.惰性删除
一个key过期后，不会立刻删除，等到下一次使用这个key时，检测到过期，此时才删除它。

缺点：若键过期了，一直没有被再次使用，会浪费内存（dict和expires字典中都要保存key-value的信息）。

## 3.定时删除
每隔一段时间，对存储键过期时间的expires字典进行检查，随机选取一些key检查过期就删除。

# 内存淘汰策略

Redis中提供了8种内存淘汰策略：

**volatile-lru：设置了过期时间的key，使用LRU算法进行淘汰**
**allkeys-lru：对所有key使用LRU算法进行淘汰**
**volatile-lfu：设置了过期时间的key，使用LFU算法进行淘汰**
**allkeys-lfu：对所有key使用LFU算法进行淘汰**
**volatile-random: 从设置了过期时间的key中随机删除**
**allkeys-random: 从所有key中随机删除**
**volatile-ttl：删除生存时间最近的一个键**
**noeviction（默认策略）：不删除键，返回错误OOM，只能读取不能写入**

通过修改 maxmemory-policy 配置来设置淘汰策略，默认noeviction

核心本质：选择哪些数据进行清理，选择什么方式去清理数据 


LRU算法(最近很少使用)

Redis3.0之后优化了LRU算法，有以下四种情况：

- 回收池已满，新采集的key与回收池中的key相比，空闲时间最小，则不做任何处理
- 回收池未满，新采集的key空闲时间比回收池中部分元素大，则插入到对应位置，后面元素往后移动一个位置
- 回收池未满，新采集的key空闲时间最小，则插入到尾部
- 回收池已满，新采集的key空闲时间比回收池中部分元素大，则移除头部元素，将新采集key插入到对应位置，前面的元素往前移动一个位置
  

LFU算法(最少使用),与LRU算法相比，引入了访问次数，但是存在时效性问题
