
# ThreadLocal
## ThreadLocal概述
线程本地对象，**为每个线程提供一个独立的变量**，和其他线程是隔离的，互不干涉的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1d8713bcf5534bb49e37a855c4b2685a.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/caa88356e9834baa8fc00a6cac34af15.png)




ThreadLocal的简单使用：
```java
public class ThreadLocalDemo {

    //创建一个ThreadLocal对象,用来为每个线程会复制保存一份变量,实现线程封闭
    private  static ThreadLocal<Integer> localNum = new ThreadLocal<Integer>(){
        @Override
        protected Integer initialValue() {
            return 0;
        }
    };

    public static void main(String[] args) {
          new Thread(){
              @Override
              public void run() {
                   //给变量设为10
                   localNum.set(10);
                  try {
                      Thread.sleep(2000);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
                  localNum.set(localNum.get()+10);//给变量+10
                  System.out.println(Thread.currentThread().getName()+":"+localNum.get());//20
              }
          }.start();

        new Thread(){
            @Override
            public void run() {
                localNum.set(-10);
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                localNum.set(localNum.get()-10);
                System.out.println(Thread.currentThread().getName()+":"+localNum.get());//-20
            }
        }.start();

        System.out.println(Thread.currentThread().getName()+":"+localNum.get());//0
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/bf43c4bdb41d4876911073f49ad1063e.png)
main线程没有对ThreadLocal变量操作，变量还是默认的0
而其他两个线程都对ThreadLocal变量进行了修改
但是修改只是线程自身使用的变量发生了变化，没有影响其他线程


## ThreadLocal原理
- **ThreadLocal**类中定义了一个静态内部类`ThreadLocalMap`
- **ThreadLocalMap**维护了一个Entry[]类型数组table
  **Entry**对象的key是**ThreadLocal**对象：`Entry(ThreadLocal<?> k, Object v)`
  一个ThreadLocal对象只能存储一个键值对，多个键值对需要多个ThreadLocal对象
 - Entry的key---**ThreadLocal是弱引用**：
    `static class Entry extends WeakReference<ThreadLocal<?>>`
    弱引用只能存活到下次垃圾回收
  - Thread类中维护了一个 `threadLocals` 属性,也就是ThreadLocalMap对象
      `ThreadLocal.ThreadLocalMap threadLocals = null;`
      每个线程都有自己的ThreadLocalMap对象，也就是每个线程都可以拥有自己的变量，其他线程无法访问，实现线程间数据隔离

ThreadLocalMap的结构
![在这里插入图片描述](https://img-blog.csdnimg.cn/6248b04ac1f44af393ee5344cacab4a5.png)
### set()方法
```java
/**
	将此线程局部变量的当前线程副本设置为指定值。
	大多数子类不需要重写此方法，仅依靠initialValue方法来设置线程局部变量的值。
	参数:value-要存储在此线程本地的当前线程副本中的值。
*/
public void set(T value) {
	//获取当前线程
    Thread t = Thread.currentThread();
    //获取当前线程的属性 ThreadLocalMap 对象
    //实现：return t.threadLocals; threadLocals就是ThreadLocalMap实例
    ThreadLocalMap map = getMap(t);
    //获取的map不为空，直接更新键值对；获取的map为空，创建map，再赋值
    if (map != null)
    	//this是ThreadLocal对象，使用ThreadLocal对象作为key
        map.set(this, value);
    else//第一次添加数据
    	//createMap实现：t.threadLocals = new ThreadLocalMap(this, firstValue);
        createMap(t, value);
}
```
创建map的方法：

```java
//创建一个ThreadLocalMap对象，赋给当前线程的map属性
void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}

//初始化一个ThreadLocalMap对象
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    table = new Entry[INITIAL_CAPACITY];
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}
```

### get()方法
```java
/**
	返回此线程局部变量的当前线程副本中的值。
	如果变量没有当前线程的值，则首先将其初始化为调用initialValue方法返回的值。
	
	@return:此线程本地的当前线程的值
 */
public T get() {
    Thread t = Thread.currentThread();//获取当前线程
    ThreadLocalMap map = getMap(t);//获取当前线程的map对象
    if (map != null) {//拿到的map不为空，获取map中存的value
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    //拿到的map为空，设置value的初始值为null，返回null
    return setInitialValue();
}
```
setInitialValue()设置初始值

```java
private T setInitialValue() {
 	T value = initialValue();//return null;
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
    return value;
}
```

### remove()方法

```java
public void remove() {
	//获得当前线程的map对象
  	ThreadLocalMap m = getMap(Thread.currentThread());
  	//map不为空，就移除该键值对
    if (m != null)
        m.remove(this);
}
```

## ThreadLocal内存泄漏问题
### 为什么会有内存泄漏问题？
首先ThreadLocalMap的Entry对象使用ThreadLocal的弱引用作为key
```java
static class Entry extends WeakReference<ThreadLocal<?>
```
- 如果ThreadLocal不存在强引用时，key:ThreadLocal肯定会被GC回收掉
- 这样就导致ThreadLocalMap中的key为null，value存在强引用的情况
- 只有该线程结束后，value的强引用链才会断掉
- 该键值对已经不使用了，但是还不能回收，造成了内存泄漏

如果线程不结束：存在这样一条强引用链
`CurrentThread 引用 -> CurrentThread 对象 -> ThreaLocalMap 对象  -> Entry 对象-> key(ThreadLocal对象)`

### 如何解决内存泄漏问题？
每次使用完ThreadLocal为线程提供的变量后，调用remove()方法，将key和value都置为null，可以被及时回收掉，解决了内存泄漏问题。
## ThreadLocal使用场景
1. 存储用户session
	
2. 数据库连接、处理事务
3. Spring使用ThreadLocal解决线程安全问题 
	Spring对一些Bean中一些非线程安全的对象采用ThreadLocal进行封装(如`RequestContextHolder`、`TransactionSynchronizationManager`、`LocaleContextHolder`...），让它们也成为线程安全的对象
	如此有状态的Bean就能够以singleton的方式在多线程中正常工作了

## ThreadLocal补充
### 1.为什么用ThreadLocal做key？而不使用Thread
`一个线程只使用一个ThreadLocalMap的话,使用Thread作为key,确实可以`

```java
public class Test{
    private static final ThreadLocal<Integer> threadLocal = new ThreadLocal<>();
}  
```

但是就算在单线程中,一个类的变量往往也不是只有一个,可能有多个

在一个多线程的应用程序中,可能有多个不同的共享资源,多个线程去访问它们

```java
public class Test{
    private static final ThreadLocal<Integer> threadLocal1 = new ThreadLocal<>();
    private static final ThreadLocal<Integer> threadLocal2 = new ThreadLocal<>();
    private static final ThreadLocal<Integer> threadLocal3 = new ThreadLocal<>();
} 
```
如果采用Thread做为ThreadLocalMap中Entry的key,那么当前线程如何准确地知道要获取哪一个ThreadLocal对象?

所以必须用 ThreadLocal 作为key,才能使用具体ThreadLocal对象的get方法准确获取数据
### 2. 先了解一下对象的几个引用,然后来看对象引用为什么ThreadLocalMap的key设计为弱引用？

#### 强引用
使用new创建的新对象，将引用指向一个变量，该变量是指向新对象的一个强引用
```java
Object obj = new Object( );
```
只要强引用对象是可触及的->就不会被GC回收掉
只要强引用对象是可达的 -> 就不会回收；

强引用是造成内存泄漏的原因之一。
#### 软引用（Soft Reference）：内存不足即回收
使用Soft Reference来管理的对象属于软引用
被软引用关联的对象只有在内存不够的时候才会被回收

```java
Object obj = new Object();// 声明强引用 
SoftReference<Object> sf = new SoftReference<>(obj); 
obj = null; //销毁强引用
```

使用场景：
高速缓存中使用软引用，还有空闲内存就暂时保留缓存；内存不够时再清理垃圾；
保证了使用缓存的同时，不会耗尽内存。


#### 弱引用（Weak Reference）发现即回收
使用Weak Reference管理的对象，只能存活到下次垃圾回收之前
垃圾回收时，不管内存够不够，一律回收弱引用的对象

```java
Object obj = new Object(); // 声明强引用 
WeakReference<Object> sf = new WeakReference<>(obj); 
obj = null; //销毁强引用
```

#### 虚引用（Phantom Reference）：对象回收跟踪
也叫“幽灵引用”；使用Phantom Reference管理的对象
需要维护一个引用队列，创建虚引用对象时需要提供一个队列作为参数

```java
Object obj = new Object(); // 声明强引用 
ReferenceQueue phantomQueue = new ReferenceQueue(); // 声明引用队列 
// 声明虚引用（还需要传入引用队列） 
PhantomReference<Object> sf = new PhantomReference<>(obj, phantomQueue); 
obj = null;
```

### 3. 为什么key设计为弱引用?
如果设计为强引用,如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/ccaa1f71df554afdb5736a60749466f6.png)
ThreadLocal如果生命周期结束,置为null后,由于key对ThreadLocal是强引用,

存在一条强引用链:
Thread -> ThreadLocalMap -> Entry[] -> Enrty -> key->ThreadLocal对象

ThreadLocal和ThreadLocalMap并不会被回收掉,产生了内存泄漏问题

因此设计为弱引用,在GC下一次清理垃圾时,就会被自动回收掉
![在这里插入图片描述](https://img-blog.csdnimg.cn/88295305f63f42528b13c1925066d065.png)
#### 测试弱引用的回收
1.WeakReference构造器中直接传入new的对象,没有其他引用
```java
public static void main(String[] args) {
    WeakReference<Object> weakReference0 = new WeakReference<>(new Object());
    System.out.println(weakReference0.get());
    System.gc();
    System.out.println(weakReference0.get());
}
```
结果: GC后弱引用会被直接回收
```java
java.lang.Object@5cad8086
null
```
2.定义一个强引用的对象,将对象传入WeakReference构造器

```java
public class TestWeakRef {
    public static void main(String[] args) {
    	//强引用指向对象
        Object object = new Object();
        //传入强引用
        WeakReference<Object> weakReference = new WeakReference<>(object);
        System.out.println(weakReference.get());
        System.gc();
        System.out.println(weakReference.get());
        //object引用->Object对象->weakReference对象
    }
}
```

结果: 没有被回收:存在强引用关系的弱引用不会被回收

```java
java.lang.Object@5cad8086
java.lang.Object@5cad8086
```
3.断开强引用对弱引用关联

```java
public class TestWeakRef {
    public static void main(String[] args) {
        Object object = new Object();
        WeakReference<Object> weakReference1 = new WeakReference<>(object);
        System.out.println(weakReference1.get());
        System.gc();
        System.out.println(weakReference1.get());

        object=null;
        System.gc();
        System.out.println(weakReference1.get());
    }
}
```
结果: 强引用=null,弱引用可以被回收掉

```java
java.lang.Object@5cad8086
java.lang.Object@5cad8086
null
```
总结: <font color='red'>强引用与弱引用同时关联一个对象,这个对象不会被GC回收掉,ThreadLocalMap.Entry的key也是一样,只有强引用断开连接,弱引用的对象才能被回收

