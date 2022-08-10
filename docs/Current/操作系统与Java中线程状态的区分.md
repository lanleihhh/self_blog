
# 操作系统中的3种or5种线程状态
## 3种
1. 就绪
2. 运行
3. 阻塞
![在这里插入图片描述](https://img-blog.csdnimg.cn/8726a9f4099748ab87e71d9470c731f8.png)

## 5种
![在这里插入图片描述](https://img-blog.csdnimg.cn/59d01d1a483a476481d99120d48349f2.png)

# Java中的6种线程状态
## 6个状态
`java.lang.Thread`类内部维护了一个枚举类State，包含了Java线程的6种状态

```java
public enum State {
	//新建
    NEW,
	//运行
    RUNNABLE,
    //阻塞
    BLOCKED,
    //等待
    WAITING,
	//超时等待
    TIMED_WAITING,
	//终止
    TERMINATED;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/107621e357d24c5b8cf098c9890a3155.png)

其中的RUNNABLE包括了**运行状态**与**就绪状态**

### NEW 新建
`new Thread()`创建了一个线程，还没有执行start()方法。此时线程状态是NEW

### RUNNABLE 运行
调用的start()，这个线程会执行它的run()方法，此时进入RUNNABLW状态
- <font color='red'>RUNNABLE 对应了操作系统中线程状态的</font> <font color='blue'>Running 和 Ready </font>
	也就是Runnable状态的线程可能正在运行，也可能处于就绪状态
- 一个正在运行的线程ThreadA，它的状态是Runnable，如果任务没执行完，CPU时间片用完了，调度其他线程，ThreadA的状态还是Runnable，处于就绪态，随时可能被再次执行。
### BLOCKED 阻塞
在进入**synchronized代码块/方法** 时，如果线程没有获取到对应的monitor锁，就会从Runnable运行状态进入Blocked阻塞状态

### WAITING 等待
**只有通过synchronized会进入阻塞Blocked**。
对于其他锁(ReentrantLock等)，如果线程没有获取到锁，会直接进入Waiting状态
本质就是执行了`LockSupport.park()`方法进入Waiting状态

wait()/join()也会进入等待

**BLOCKED阻塞状态 与 WAITING等待状态的区别**
- Blocked：没有获取到monitor锁，进入阻塞，等待其他线程释放monitor锁 
- Waiting：等待某个条件，调用了notify()/notifyAll()，或者join的线程执行完
### TIMED_WAITING 超时等待
线程会进入超时等待的条件：执行以下带时间参数的方法时

|                    方法                    |                          方法签名                           | 所属类      |
| :----------------------------------------: | :---------------------------------------------------------: | ----------- |
|            `wait(long timeout)`            |         public final native void wait(long timeout)         | Object      |
|            `sleep(long millis)`            |        public static native void sleep(long millis)         | Thread      |
|            `join(long millis)`             |      public final synchronized void join(long millis)       | Thread      |
|  `parkNanos(Object blocker, long nanos)`   |  public static void parkNanos(Object blocker, long nanos)   | LockSupport |
| `parkUntil(Object blocker, long deadline)` | public static void parkUntil(Object blocker, long deadline) | LockSupport |

LockSupport的park和unpark方法都是调用Unsafe类的本地方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/da4ef6383cf9422ab09c1e82871e280c.png)	
	

### TERMINATED 终止
1. run()方法执行完，线程结束
2. 程序出现异常，意外终止了run()方法

## 线程状态的切换
### 从 Blocked 进入 Runnable 状态
Runnable进入Blocked是因为没有获得锁才进入了阻塞；
所以被阻塞的线程获得其他线程释放的monitor锁后，才可以从Blocked到Runnable

### 从 Waiting 进入 Runnable状态
1. 执行LockSupport.unpark()
2. join的线程结束
3. 被中断


- 如果通过其他线程调用**notify()或 notifyAll()来唤醒它，那么它会直接进入Blocked** 状态这里需要注意一点，因为唤醒 waiting线程的**线程如果调用notify/notifyAll**，要求**必须先持有该 monitor锁**，也就是wait、notify、notifyAll 必须在synchronized 代码块中。
	<img src='https://img-blog.csdnimg.cn/ccfb8bf8a6d4451d9b02204c318fd673.png' width='400px'/>

- 所以处于 waiting 状态的线程被唤醒时拿不到该锁，就会进入 Blocked 状态，直到执行了notify/notifyAll 唤醒它的线程执行完并且释放了 monitor锁，才可能轮到它去尝试获取这把锁，如果它得到了锁，就会从Blocked 状态进入Runnable状态。

### 从 TIMED_WAITING进入 Runnable状态
1. 通过notify/notifyAll先进入阻塞Blocked，获取到锁后，再进入Runnable状态
2. 超过设置的等待时间后，就会自动获取锁，直接进入Runnable
	 join线程执行完 / LockSupport.unpark() / 被中断 都会直接进入Runnable不会经历Blocked阻塞



