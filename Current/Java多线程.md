# Java多线程

## 1.1  概念

多线程指的是程序中包含多个不同的执行单元,一个程序中可以允许多个线程执行不同的任务

### 1.2 使用环境

1. 程序需要执行两个及以上的任务
2.  需要实现一些等待的任务(输入,文件读写,网络,搜索...)
3.  需要一些后台运行的程序

### 1.3优缺点

优点:

- 提高程序的响应. 

-  提高CPU的利用率.

- 改善程序结构,将复杂任务分为多个线程,独立运行.

缺点:

- 线程越多占用内存越多
- 多线程需要协调和管理，所以需要CPU时间跟踪线程 
-  多个线程之间对共享资源的竞用访问,需要解决这个问题

## 2.线程同步

### 2.1并发与并行

并行与并发
- **单核** cpu 下，线程实际是**串行执行**的。操作系统中有一个组件叫做**任务调度器**，将 cpu 的时间片,分给不同的线程使用，只是由于 cpu 在线程间（时间片很短）的切换非常快，人类感觉是同时运行的。 总结为一句话就是：**微观串行，宏观并行**，一般会将这种线程轮流使用 cpu 的做法称为**并发**(**concurrent**)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/e6ecc7de819645a999d0ffe25035ac3a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_14,color_FFFFFF,t_70,g_se,x_16)
- **多核** cpu 下，每个核（core）都可以调度运行线程，这时候线程可以是**行**的
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/5675f0df19354cbbb96610f99a4c6d0d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_15,color_FFFFFF,t_70,g_se,x_16)
  如何理解并发?
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2a1a52aed1704fbf94cf94335f617cbd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_16,color_FFFFFF,t_70,g_se,x_16)
- 大家排队在一个咖啡机上接咖啡，`交替执行，是并发`；两台咖啡机上面接咖啡， 是`并行`
- 从严格意义上来说，**并行**的多任务是真的**同时执行**，而对于**并发**来说，这个过程只是**交替执行**的，一会执行任务 A，一会执行任务 B，系统会不停地在两者之间切 换。

- `并发说的是在一个时间段内，多件事情在这个时间段内交替执行`。
- `并行说的是多件事情在同一个时刻同事发生`。


### 2.2多线程同步

多线程同时读取同一份共享资源时,可能会引起冲突,需要引入线程"同步机制"(即各个线程之间有先来后到的顺序)

==同步: 排队 + 锁==

**排队** : 对共享资源操作时,几个线程之间要排队,不能同时操作

**锁**:      保证数据在方法中被访问时的正确性,在访问时加入锁机制

## 3同步锁:

### 3.1 synchronized关键字

Java中synchronized关键字同步方法或代码块

1. synchronized(同步对象){需要被同步的代码块}

2. synchronized 返回值类型 方法名(){需要 被同步的代码}   适用于实现了Runnable接口的类
3. static synchronized 返回值类型 方法名(){需要 被同步的代码}   适用于继承Thread的类

我们用一个窗口售票的程序来演示

1. 继承Thread的方式

为代码块添加同步锁

```java
/*
    售票线程
*/
public class TicketThread extends Thread{

    //使用static关键字,将成员变量设为整个类的共享资源
    static int num = 10;//剩余票数
    static Object obj = new Object();//保证只有一个
    @Override
    public void run() {
        while (true){
            //synchronized 同步锁,就像一个对象,但要求对象只有一个线程
            synchronized (obj){
                if(num>0){
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()+"剩余票:"+num);
                    num--;
                }else {
                    break;
                }
            }  //synchronized同步代码块,执行完成后,同步对象(锁)会自动释放
        }
    }
}
```

为方法添加同步锁

```java
/*
    售票线程
*/
public class TicketThread extends Thread{

    //使用static关键字,将成员变量设为整个类的共享资源
    static int num = 10;//剩余票数
    static Object obj = new Object();//保证只有一个
   
	@Override
	public void run() {
	  while (true){
	      if(num>0){
	          print();
	      }else{
	          break;
	      }
	  }
	}
   /*
        synchronized修饰方法时,同步对象锁,默认是this
        一旦创建多个线程对象  用static 修饰同步方法,同步对象-->线程类
        这种情况只针对继承了Thread的类(Thread的类实例化一个线程对象,就执行一个单独的任务,不加static,多个线程之间对共享资源num 的使用有冲突)
    */
   public  static synchronized   void  print(){
       if(num>0){
           try {
               Thread.sleep(1000);//休眠1s
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           System.out.println(Thread.currentThread().getName()+"剩余票:"+num);
           num--;
       }
   }
```

```java 
public class TicketThreadTest {
    public static void main(String[] args) {

        //创建两个线程对象   执行两个任务
        TicketThread t1 = new TicketThread();
        TicketThread t2 = new TicketThread();
        t1.setName("窗口1");
        t2.setName("窗口2");

        t1.start();
        t2.start();
    }
}
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021070420192738.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


2. 实现Runnable的方式(同步代码块)

```java
/*
    Thread   :  多个线程对象,执行多个任务
    Runnable :  多个线程对象,执行一个任务
 */
public class TicketRunnable implements Runnable{

    //使用static关键字,将成员变量设为整个类的共享资源
    static int num = 10;//剩余票数

    @Override
    public void run() {
        while (true){
          
            /*方式一
            synchronized(){要同步的代码块}
            添加同步对象,这种方式可以使用this表示锁对象(Runnable接口的实现类这样使用,Thread的子类需要创一个静态的对象,使用静态对象作为同步对象)
            */
            synchronized (this){
                if(num>0){
                    try {
                        Thread.sleep(1000);//休眠
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    //输出剩余票数
                    System.out.println(Thread.currentThread().getName()+"剩余票:"+num);
                    num--;//票数-1
                }else{
                    break;
                }
            }//同步代码块,执行完成后,同步对象(锁)会自动释放
        }
    }
}

```

同步方法

```java

/*
    Thread   :  多个线程对象,执行多个任务
    Runnable :  多个线程对象,执行一个任务
 */
public class TicketRunnable implements Runnable{
    //使用static关键字,将成员变量设为整个类的共享资源
	static int num = 10;//剩余票数

@Override
public void run() {
    while (true){
        if(num>0){//票数>0时输出剩余票数
            print();
        }else{
            break;//<0退出
        }
    }
}

//在方法声明中添加synchronized同步监视器,表示整个方法为同步方法
public synchronized void print(){
    if(num>0){
        try {
            Thread.sleep(1000);//休眠
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+"剩余票:"+num);
        num--;//票数-1
    }
}
```
```java
//测试类
public class TicketRunnableTest {
    public static void main(String[] args) {
        //创建一个任务对象
        TicketRunnable ticketRunnable  = new TicketRunnable();

        //创建两个线程,执行同一任务
        Thread t1 = new Thread(ticketRunnable,"窗口1");
        Thread t2 = new Thread(ticketRunnable,"窗口2");

        t1.start();
        t2.start();
    }
}
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704202129251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


### 3.2  Lock接口

JDK5.0开始,java提供了更强大的线程同步机制,通过显式定义同步锁对象

同步锁使用Lock对象 ,每次只能有一个线程对Lock对象加锁,线程访问共享资源之前要先获得Lock对象

#### ReetrantLock 
ReetrantLock是Lock的实现类,可以显式的添加/释放锁

```java
package day2.demo2;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class LockThreadDemo1 implements Runnable{

    //使用static关键字,将成员变量设为整个类的共享资源
    static int num = 10;//剩余票数
    Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true){
            if(num>0){
                print();
            }else{
                break;
            }
        }
    }

    public   void  print(){
        try {
            lock.lock();//上锁
            try {
                Thread.sleep(1000);//休眠1s
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            if(num>0){
                System.out.println(Thread.currentThread().getName()+"剩余票:"+num);
                num--;
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();//解锁,放在finally中,锁一定会被释放
        }
    }
}
```

```java
public class LockThreadDemo1Test {
    public static void main(String[] args) {

        LockThreadDemo1 l = new LockThreadDemo1();

        //两个线程执行一个任务  l
        Thread t1 = new Thread(l);
        Thread t2 = new Thread(l);

        t1.start();
        t2.start();
    }
}
```

运行结果:



## 4线程死锁
### 线程死锁的四个条件
1. **互斥**：同一份资源，一次只能被一个线程使用
2. **请求并保持**：请求需要的资源(等待其他线程释放资源)，同时保持自身已持有的资源
3. **不可剥夺**：A线程持有资源时，其他线程不能获得该资源
4. **循环等待**：所有请求资源的线程形成环路，死循环，都不会释放资源，造成线程永久阻塞

 **死锁**： 不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃 自己需要的同步 资源，就形成了线程的死锁. 出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于 阻塞状态，无法继续. 

```java
public class LockThreadDemo2 extends Thread{
    static Object objA = new Object();
    static Object objB = new Object();

    boolean flag;
    public LockThreadDemo2(boolean flag) {
        this.flag = flag;
    }

    @Override
    public void run() {
        if(flag){
            synchronized (objA){
                System.out.println("If_objA");

                synchronized (objB){
                    System.out.println("If_objB");
                }
            }
        }else {
            synchronized (objB){
                System.out.println("Else_objB");
                synchronized (objA){
                    System.out.println("Else_objA");
                }
             }
        }
    }
}
```

```java
public class LockThreadDemo2Test {
    public static void main(String[] args) {
        LockThreadDemo2 l1 = new LockThreadDemo2(true);
        LockThreadDemo2 l2 = new LockThreadDemo2(false);

        l1.start();
        l2.start();
    }
}
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704202158185.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


解决方法:

 设计时考虑清楚锁的顺序，尽量减少嵌套的加锁交互数量 

## 5. 线程通信

### 5.1交替打印1~100的案例:

```java
package day2.demo2;
/*
    wait()      当前线程阻塞,释放同步监视器
    notify()    唤醒被wait的一个或多个线程(先唤醒优先级最高的)
    notifyAll() 唤醒所有被wait的线程
 */
public class PrintNum extends Thread{
    public static void main(String[] args) {

        PrintNum p1 = new PrintNum();
        PrintNum p2 = new PrintNum();
        p1.start();
        p2.start();
    }

    private static int num=0;
    private static Object obj = new Object();

    @Override
    public void run() {

        while (true){
            /*
            此处synchronized()内不能使用this,因为该类继承了Thread类
            创建线程对象会有不同的任务
             */
            synchronized (obj){//为代码块加锁
                obj.notify();//唤醒等待的线程,由于已经有线程中持有锁了,也是不能进入同步代码中
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if(num<100){
                    System.out.println(Thread.currentThread().getName()+":"+(++num));
                }else {
                    break;
                }
                try {
                    obj.wait();//线程等待,释放锁
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

运行结果:  可以看到两个线程之间交替打印数字

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704202207287.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704202213589.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


### 5.2生产者/消费者问题

 生产者(Productor)将产品放在柜台(Counter)，而消费者(Customer)从柜台 处取走产品，生产者一次只能生产固定数量的产品(比如:1）， 这时柜台中不能 再放产品,此时生产者应停止生产等待消费者拿走产品,此时生产者唤醒消费者来 取走产品,消费者拿走产品后,唤醒生产者,消费者开始等待 

```java
package day2.demo3;
/*
    柜台
 */
public class Counter {

    //柜台商品数,所有线程共享
    private static int num=1;

    //生产
    //同步对象锁是this,(同一个Counter对象)
    public synchronized void add(){
        while (true){
            this.notify();//唤醒消费者线程
            if(num<1){//当商品数不足一时,开始生产(++)
                num++;
                System.out.println("生产了"+num+"个");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }else {
                try {
                    wait();//阻塞生产者线程
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //消费
    public synchronized void sub(){
        while (true){
            this.notify();//唤醒生产者线程
            if(num>0){//当商品数>0时,开始消费(--)
                System.out.println("消费了"+num+"个");
                num--;
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }else{
                try {
                    this.wait();//阻塞消费者线程
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

```java
//消费者线程
public class ConsumerThread extends Thread {
    Counter counter;
    
    //构造方法
    public ConsumerThread(Counter counter) {
        this.counter = counter;
    }

    @Override
    public void run() {
            counter.sub();
    }
}
```

```java
//生产者线程
public class ProducterThread extends Thread {

    Counter counter;

    //构造方法
    public ProducterThread(Counter counter) {
        this.counter = counter;
    }

    @Override
    public void run() {
            counter.add();
    }
}

```

```java
package day2.demo3;

public class Test {

    public static void main(String[] args) {
        //新建Counter任务对象
        Counter counter = new Counter();

        //新建消费者线程
        ConsumerThread consumerThread = new ConsumerThread(counter);
        //新建生产者线程
        ProducterThread producterThread = new ProducterThread(counter);

        consumerThread.start();//启动消费者线程
        producterThread.start();//启动生产者线程
    }
}

```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704202224464.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




