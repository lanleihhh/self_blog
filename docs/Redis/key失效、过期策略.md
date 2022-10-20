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
