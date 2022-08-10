# Java线程


## 1、程序、进程、线程

程序、进程、线程

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628222621269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


在主线程中可以创建并启动其他的线程

一个进程内的所有线程共享该进程的内存资源

## 2、单线程路径

main()方法也属于线程（主线程），main（）中的全部代码可视为一个线程所要执行的

```java
public class Sample {
    public static void main(String[] args) {
        
        // 按照顺序依次执行
        System.out.println("main--start");
        method1();
        System.out.println("main--end");
    }
	//方法1
    private static void method1() {
        method2();//调用方法2
        System.out.println("method1--start");
    }
	方法2
    private static void method2() {
        System.out.println("method2--start");
    }
    
    /*
    运行结果:
    
    main--start
    method2--start
    method1--start
    main--end
    */
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062822265158.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 3、线程创建

### 3.1继承Thread类

```java
public class MyThread extends Thread {

    //重写Thread类的run（）方法
    @Override
    public void run() {
        for (int i = 0; i < 1000; i++) {
            System.out.println("MyThread:"+i);
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        
        MyThread myThread = new MyThread();//创建线程对象
        myThread.start();//启动线程
        //myThread.run();//启动线程不能调用run()方法,否则与普通方法无异(不是独立线程)
        for (int i = 0; i < 1000; i++) {
            System.out.println("main:"+i);
        }
    }
}
```



### 3.2实现Runnable接口

```Java
public class MyThread implements Runnable{

    //实现run()
    @Override
    public void run() {
        for (int i = 0; i < 10000; i++) {
            System.out.println("MyThread:"+i);
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {

        //线程要执行的任务
        MyThread myThread = new MyThread();//任务对象
        Thread thread = new Thread(myThread);//创建线程，并为线程指定任务对象
        thread.start();//启动线程
        
        for (int i = 0; i < 1000; i++) {
            System.out.println("main:"+i);
        }
    }
}
```

运行结果： 可以看到线程thread与其他交替执行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628222718614.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




继承Thread与实现Runnable的区别;

继承Thread       线程代码放在Thread子类run()方法内

实现Runnable   线程代码放在实Runnable的实现类的run()方法内

实现Runnable的好处：

- 避免了单继承的局限性
- 多个线程可以共享同一个接口实现类的对象，适合多个相同线程来处理同一份资源

### 3.3实现Callable接口

```java
package day2.demo4;

import java.util.concurrent.Callable;

public class CallableDemo implements Callable<Byte> {


    /*
        实现Callable与Runnable接口相比
        call()相比于run():
        可以有返回值
        方法可以抛出异常
        支持泛型的返回值
        借助FutureTask类，获取返回结果
     */
    @Override
    public Byte call() throws Exception {

        for (int i = 0; i < 10; i++) {
            System.out.println(i);
        }
        return 10;
    }
}
```

```java

import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;
import java.util.concurrent.FutureTask;
/*
	测试类
*/
public class Test {
    public static void main(String[] args) {

        CallableDemo callableDemo = new CallableDemo();
        //接收任务
        FutureTask<Byte> f = new FutureTask(callableDemo);
        //创建线程
        Thread t = new Thread(f);

        t.start();
        try {
            Byte res = f.get();//获取call()的返回值
            System.out.println("返回值:"+res);//
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```


运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210704202254416.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)



## 4、Thread类常用方法

### 4.1 Thread 类构造方法

 Thread（）创建线程对象

Thread（String name）指定名称

Thread（Runnable target）使用Runnable对象

Thread（Runnable target,String name）使用Runnable对象，并指定名称

### 4.2常用方法

|                            方法                            |                             作用                             |
| :--------------------------------------------------------: | :----------------------------------------------------------: |
|                       void start(）                        |                           启动线程                           |
|              final void setName(String name)               |                        设置线程的名称                        |
|                   final String getName()                   |                        返回线程的名称                        |
|          final void setPriority(int newPriority）          |                       设置线程的优先级                       |
|                  final int getPriority()                   |                       返回线程的优先级                       |
|       final void join() throws InterruptedException        |                         等待线程死亡                         |
|               static Thread currentThread()                |              返回对当前正在执行的线程对象的引用              |
| static void sleep(long millis) throws InterruptedException | 让当前正在执行的线程休眠（暂停执行）,参数为毫秒（1000ms=1s） |
|  static void yield()| 线程让步,让出CPU的使用权） |

###    4.3优先级

#### 4.3.1优先级

优先级为整数（1~10）

一般默认值为5

通过setPriority（），getPriority()设置/返回优先级

#### 4.3.2调度策略

CPU轮番调度执行进程

时间片：  给线程分配时间单位去执行

抢占式;    优先级高的线程有更多的执行权，但不是优先级低的就没有机会

#### 4.3.3优先级静态常量

 Thread类有如下3个静态常量来表示优先级 

-  MAX_PRIORITY：取值为10，表示最高优先级。 

-  MIN_PRIORITY：取值为1，表示最底优先级。 

- NORM_PRIORITY：取值为5，表示默认的优先级。 

```java
public class MyThread implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            //currentThread()返回当前正在执行对象的引用
            //getName():返回线程名
            System.out.println(Thread.currentThread().getName()+":"+i);
        }
    }
}
```

```java 
//测试类
public class MyTreadTest {
    public static void main(String[] args) {

        MyThread mt = new MyThread();
        /*
        Thread(Runnable target,name)
         */
        Thread t1 = new Thread(mt,"t1");
        Thread t2 = new Thread(mt,"t2");
        //设置优先级
        t1.setPriority(5);
        t2.setPriority(10);
        //返回优先级
        System.out.println(t1.getName()+"优先级为："+t1.getPriority());
        System.out.println(t2.getName()+"优先级为：："+t2.getPriority());
        //启动进程
        t1.start();
        t2.start();

        for (int i = 0; i < 20; i++) {
            System.out.println(Thread.currentThread().getName()+":"+i);
        }
    }
}

```

运行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628222739980.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


```java
public class MyThread implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            //currentThread()返回当前正在执行对象的引用
            //getName():返回线程名
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            if(i%10==0){
                Thread.yield();//线程让步
            }

            System.out.println(Thread.currentThread().getName()+":"+i);
        }
    }
}
```

注意：`Thread`中的`public void run()`  和`Runnable` 中的`public abstract void run()` ==不抛出异常==，重写或实现run()时，必须将其中可能出现异常的代码块放入==try-catch==中

```java
//测试类
public class MyTreadTest {
    public static void main(String[] args) throws InterruptedException {
        MyThread mt = new MyThread();
        Thread t1 = new Thread(mt,"t1");
        Thread t2 = new Thread(mt,"t2");

        t1.start();//启动线程
        t1.join();//等待该线程死亡,其他线程进入阻塞状态
        t2.start();//启动线程
        t2.sleep(2000);//休眠2s

        for (int i = 0; i < 100; i++) {
            Thread.sleep(500);//休眠0.5s
            System.out.println(Thread.currentThread().getName()+":"+i);
        }
    }
}

```

运行结果：  可以看到，t1线程被执行完毕，才执行其他线程（t1.join()时，其他线程都被阻塞，必须等t1执行完毕才执行其他）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628222759231.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 5.线程状态（生命周期）

1. 新建  

   new Thread（） 创建线程对象

2. 就绪

   start（）  并不会立即执行，进入线程队列等待CPU的加载（获取CPU使用权）

3.  运行

   就绪的线程被调度并取得CPU使用权，开始执行线程（run()方法定义了线程的操作和功能）

4.  阻塞

   被人为挂起/输入输出，让出CPU并临时终止自己的执行

5. 死亡

   完成工作或线程被提前强制终止或出现异常，导致结束

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628222820472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 6、线程分类

### 6.1 用户线程与守护线程

用户线程：平时用到的普通线程均是用户线程，当在Java程序中创建一个线程，它就被称为用户线程 

守护线程： 为其他线程的运行提供便利服务，守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者 

区别：

当前JVM中有任一用户线程未结束，守护线程全部工作

最后一个用户线程结束时，守护线程随 JVM 一起离开

注意：设置线程为守护线程必须在此线程启动之前，否则会抛出 IllegalThreadStateException 异常

```java
setDaemon(true)//设置该线程为守护线程
```

```java
package demo6;
public class Test {
    public static void main(String[] args) {

        UserThreadDemo user = new UserThreadDemo();
        DaemonThreadDemo daemon = new DaemonThreadDemo();

        Thread t1 = new Thread(user,"user" );
        t1.start();

        Thread t2 = new Thread(daemon,"daemon" );
        t2.setDaemon(true);//设置该线程为守护线程，必须在启动前设置
        t2.start();
    }
}
/*
    用户线程
 */
class UserThreadDemo implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            try {
                Thread.sleep(1000);//睡眠1s
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+":"+i);
        }
    }
}

/*
    守护线程
 */
class DaemonThreadDemo implements Runnable {

    @Override
    public void run() {
        while (true){
            try {
                Thread.sleep(2000);//设置每执行一次休眠2s时间，观察守护线程
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("守护中...");
        }
    }
}
```

运行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062822284741.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)





