## ConcurrentHashMap
JDK5 增加 多线程并发安全的HashMap(锁分段/独占锁)
JDK8 修改为 CAS + synchronized

>ConcurrentHashMap不像HashTable一样对整个put方法加锁，而是将每一个位置(Node)作为一个独立空间对其加锁，锁粒度变小，提高了并发访问效率

### ConcurrentHashMap如何保证线程安全？
1. 在进入put方法后，通过hash值计算添加的位置
2. 若位置上没有元素，采用cas机制判断，添加元素
3. 若位置上有元素了，使用链表第一个Node作为锁标记的对象(使用synchronized)




### HashMap、HashTable、ConcurrentHashMap
- HashMap线程不安全，允许键和值为null
- HashTable线程安全，在put方法上加锁了，并发下只能有一个线程进入put，不允许键和值为null

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/9bc7a61968d641aaaa1272967e5d7809.png)
- ConcurrentHashMap线程安全，不允许键和值为null
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/4d0420f2523b4a5fa2fc7ae7ab4544cf.png)

### ConcurrentHashMap为什么不能存key和value为null？
- 不能存key为null：不能判断key为null  还是key没找到
- 不能存value为null：get()时不能判断是存入的value为空，还是map中没有改key-value的映射

```java
public class ConcurrentHashMapDemo {
    /*
       HashMap是线程不安全的,不能并发操作的
       ConcurrentModificationException  并发修改异常   遍历集合,并删除集合中的数据

       Hashtable 是线程安全的 public synchronized V put(K key, V value)-->独占锁
            锁直接加到了put方法上,锁粒度比较大,效率比较低
            用在低并发情况下可以

       Map<String,Integer> map = Collections.synchronizedMap(new HashMap<>());
       ConcurrentHashMap
     */
    public static void main(String[] args) {

        Map<String, Integer> map = new ConcurrentHashMap<>();
        //Map<String,Integer> map = new HashMap<>();
        //Map<String,Integer> map = new Hashtable<>();
        //模拟多个线程对其操作
        for (int i = 0; i < 20; i++) {
            new Thread(
                    () -> {
                        map.put(Thread.currentThread().getName(), new Random().nextInt());
                        System.out.println(map);
                    }
            ).start();
        }
    }
}
```

## CopyOnWriteArrayList
>ArrayList是线程不安全的；
>Vector是线程安全的；但是给add()和get()都加了锁，一个线程写的时候or读的时候，其他线程不能去读，效率低下

CopyOnWriteArrayList只有写-写时，才会阻塞，可以并发读取(读操作没有锁)

可变操作 add()、set()等修改数据的操作，会先创建一个底层数组的副本，将要添加或修改的数据写入副本，用副本替换底层数组
![在这里插入图片描述](https://img-blog.csdnimg.cn/247777db82524daf9855009e05702011.png)

```java
/**
 * ArrayList 并发情况下不能使用,不能同时有多个下次对其操作
 * Vector 是线程安全的  对 读/取 方法加了锁
 * 读和写用的同一把锁  <-- synchronized修饰非静态方法,默认同步对象是this
 * CopyOnWriteArrayList
 * 对 读 和 写 操作分离,读操作不用加锁,
 * 写操作加锁,写时不影响读操作
 * 多个线程同时添加时会互斥
 * 添加时先将原来的数组复制一个副本,将数据添加到副本中,不影响读操作,最后再用副本替换原数组
 */
public class CopyOnWriteArrayListDemo {
    public static void main(String[] args) {
        //List<Integer> list = new ArrayList();//java.util.ConcurrentModificationException
        List<Integer> list = Collections.synchronizedList(new ArrayList<Integer>());//转为线程安全的集合
        //List<Integer> list = new Vector();
        //List<Integer> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10; i++) {//模拟10个线程对其操作
            new Thread(
                    () -> {
                        list.add(new Random().nextInt());
                        System.out.println(list);
                    }
            ).start();
        }
    }
}
```

## CopyOnWriteArraySet
底层用CopyOnWriteArrayList实现
![在这里插入图片描述](https://img-blog.csdnimg.cn/5d566a579879499eb14732c0a29e0bb0.png)
在添加数据时，会控制不出现重复数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/4a7ee9d6df924f92853619df9951c12f.png)

```java
/**
 * CopyOnWriteArraySet
 * 不允许重复数据出现的单列集合
 * 底层是CopyOnWriteArrayList实现
 * 添加时判断是否重复
 */
public class CopyOnWriteArraySetDemo {
    public static void main(String[] args) {
        Set<Integer> set = new CopyOnWriteArraySet<>();
        for (int i = 0; i < 10; i++) {
            new Thread(
                    () -> {
                        set.add(new Random().nextInt());
                        System.out.println(set);
                    }
            ).start();
        }
    }
}
```

## CountDownLatch
- 减法计数辅助类，允许一个线程等待其他线程执行完后再执行
- **底层使用AQS实现**
1. new CountDownLatch(线程数量)，指定的线程数量将会传给AQS中的state，每执行完一个线程，计数器-1-->AQS的state - 1
2.  state为0时，所有线程执行完了，关闭计数器，再去执行指定的线程

```java
public class CountDownLatchDemo {
    /**
     * 辅助类
     * 使一个线程 等待其他线程执行完毕后再执行
     * 相当于一个程序计数器 是一个递减的计数器
     * 先指定一个(线程)数量,当有一个线程执行结束后,就减一 ,直到减为0 关闭计数器
     * 此时线程就可以执行了
     */
    public static void main(String[] args) throws InterruptedException {

        CountDownLatch countDownLatch = new CountDownLatch(10);
        for (int i = 0; i < 10; i++) {
            new Thread(
                    () -> {
                        System.out.println(Thread.currentThread().getName());
                        countDownLatch.countDown();//计数器减一操作
                    }
            ).start();
        }
        countDownLatch.await();//关闭计数器
        System.out.println("main线程");
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/77ce60d26c6b4ef980dfa8c51d448153.png)
如果不使用辅助类CountDownLatch，线程将是随机执行的
## CycliBarrier
- 加法计数辅助类，让一组线程都到达某一屏障时，必须等最后一个线程到达屏障，才开始执行
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/b63bcf08a10f477d8668de67404e85fd.png)

```java
public class CyclicBarrierDemo {

    /*
       CyclicBarrier 让一组线程到达一个屏障时被阻塞，直到最 后一个线程到达屏障时，屏障才会开门
         是一个加法计数器,当线程数量到达指定数量时,开门放行
     */

    public static void main(String[] args) {
        CyclicBarrier c = new CyclicBarrier(5, () -> {
            System.out.println("大家都到齐了 该我执行了");
        });

        for (int i = 0; i < 5; i++) {
            new Thread(
                    () -> {
                        System.out.println(Thread.currentThread().getName());
                        try {
                            c.await();//加一计数器
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        } catch (BrokenBarrierException e) {
                            e.printStackTrace();
                        }
                    }
            ).start();
        }
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/4e579b4d8cd14692ae5e8dda4a2c0ed8.png)

