# Linux 内核参数VM调优

## 1. pdflush刷新脏数据条件

>cached中的脏数据满足如下几个条件中一个或者多个的时候就会被pdflush刷新到磁盘：
>
>**（1）**数据存在的时间超过了dirty_expire_centisecs（默认30s）时间，触发pdflush
>
>**（2）**脏数据所占内存 /（MemFree + Cached - Mapped） > dirty_background_ratio  ，触发pdflush
>
>​		 



## 2. 参数调优

**如果系统的cached中脏数据量很大，会产生两个问题：**

**a.**  缓存的数据越多，丢数据的风险越大。

**b.**  会定期出现IO峰值，这个峰值时间会较长，在这期间所有新的写IO性能会很差（极端情况直接被hang住）。

后一个问题对写负载很高的应用会产生很大影响。



**如何调节内核IO参数来优化IO写性能？**

1. **首先调优dirty_background_ratio——脏数据刷新的频率，从而减少IO峰值；**

   这个参数适当调小，这样可以使得cached主的脏数据减少，把原来一个大的IO刷新操作变为多个小的IO刷新操作，从而把IO写峰值削平。对于cached很大或者磁盘很慢的场景，应该把这个值设置的小一点。调节方法：echo 5 >> /proc/sys/vm/dirty_background_ratio 把这个百分比更新为5% （注意这里所占的百分比是针对于：MemFree + Cached - Mapped而言的，并不是相对于MemTotal）

2. **第二步调节dirty_ratio参数：限制脏数据占用磁盘空间比例、超过参数后会停止所有写IO**

   这个参数适当调小。如果cached的脏数据所占比例（这里是占MemTotal的比例）超过这个设置，系统会停止所有的应用层的IO写操作，等待刷完数据后恢复IO。所以万一触发了系统的这个操作，对于用户来说影响非常大的

3. **第三步调节dirty_expire_centisecs参数（这个参数表示page cache中的数据多久标记为脏）**

   这个参数调节可能意义不大。调小这个参数并不保证可以很快的把脏数据刷新下去，因为这里会有个IO拥塞问题。如果在一个dirty_expire_centisecs周期内没有刷完脏数据就会导致这个参数失效了。理想情况我们希望一个dirty_expire_centisecs刷完脏数据，但如果cached的脏数据较多或者磁盘较慢的时候就会导致IO拥塞问题。一般使用默认值就好。

4. **第四步调节dirty_writeback_centisecs参数（这个参数调节pdflush被唤醒的频率）**

   调小这个参数，可以提高pdflush工作频率，从而尽快把脏数据刷新到磁盘上。但是这一样会遇到第三步IO拥塞问题。所以这个参数效果也不尽如人意。一般使用默认值就好。

## 3.Swapping调优

swap空间是一块磁盘空间，操作系统使用这块空间，保存从内存中换出的操作系统不常用的page数据，这样可以分配处更多的内存做page cache。这样通常会提升系统的吞吐量和IO性能，但同样会产生很多问题。页面频繁换入换出会产生IO读写、操作系统中断，这些都很影响系统的性能，这个指越大，操作系统就会更加积极地使用swap空间



**调节swappniess方法如下：**

```shell
cat /proc/sys/vm/swappniess #查看这个参数的配置（默认值是60）

echo 0 >> /proc/sys/vm/swappniess #禁止操作系统使用任何的swap空间

echo 100 >> /proc/sys/vm/swappniess #操作系统会尽量使用swap空间
```

swappniess设置一个适当值对于系统性能也会有明显的影响。

swappniess很小时，系统能并发的进程或者线程就会减少，但每个进程或者线程运行的速度较快，cpu利用率较好。

swappniess很大时，系统并发好，但每个进程或者线程速度较慢。较多IO读写和系统中断会消耗很多cpu资源，此时系统效率较低。

所以如果希望提高服务器的并发量，对服务的相应时间要求不很高的场景可以适当的把swappniess调节的高些。对于并发量不大但希望相应时间小的应用场景可以适当的调小这个参数，比如个人电脑可以直接禁掉swap。