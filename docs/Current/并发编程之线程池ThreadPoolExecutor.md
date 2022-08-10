
# 线程池
## 概述
>和JDBC的数据库连接池类似
数据库连接池：
初始化时创建多个数据库连接，请求数据库时，直接使用，响应快
请求任务完成后，连接对象并不会销毁，就放在池中统一管理
减少了频繁创建和销毁带来的开销

线程池：
统一管理多个线程，来任务后创建线程去执行，执行完后不会销毁，用来处理后续新来的任务。
- **重复利用**线程，减少创建线程和销毁的开销
- **统一管理**线程，线程的创建/销毁由线程池管理
- **提高响应速度**，任务来时直接使用线程去处理，不用创建线程
## ThreadPoolExecutor

### 使用方法
1. **创建ThreadPoolExecutor对象，传入指定参数**
	![在这里插入图片描述](https://img-blog.csdnimg.cn/1f0182b00250483095aa322f95529987.png)
2. 执行Runnable任务时，调用`execute()`来执行
3. 执行Callable任务时，如果需要返回结果，调用`submit`方法执行，`submit()与call()都有返回值`

### 构造器7个参数

```java
public ThreadPoolExecutor(
	int corePoolSize,                  //核心线程数
	int maximumPoolSize,         	   //最大线程数
	long keepAliveTime,               //非核心线程的存活时间
	TimeUnit unit,                    //存活时间的单位
	BlockingQueue<Runnable> workQueue,//阻塞队列
	ThreadFactory threadFactory,      //线程工厂
	RejectedExecutionHandler handler  //拒绝策略
){}
```

- corePoolSize：核心线程池大小，默认初始化为0，有任务到达才去创建线程，任务执行完线程不销毁
- maxinumPoolSize：线程池最大线程数。表示在线程池中最多能创建多少个线程
- keepAliveTime：表示非核心线程池没有任务执行时，多长时间线程会销毁
- unit：给keepAliveTime指定时间单位
- workQueue：核心线程池满后，其他任务会进入一个阻塞队列，等待被执行
- ThreadFatory：创建线程的线程工厂
- handler：拒绝策略；表示当核心池线程数  +  非核心线程线程数 == 最大线程数**maximumPoolSize**时，对新来任务的拒绝策略


### 线程池如何执行？

![在这里插入图片描述](https://img-blog.csdnimg.cn/ba747077bdb546d2bb38ebfc34a4b945.png)


1. 核心池中存活线程数 < corePoolSize时，创建新的线程去执行任务，执行完线程不会销毁
2. 如果核心池线程数已满，新提交的任务，会放进workQueue中排队等待执行
3. 核心池线程数已满，workQueue已满，判断线程数是否达到maximumPoolSize，没有到达就创建一个非核心线程去执行新任务
4. 当前线程数 == maximumPoolSize 时，新的任务采用拒绝策略处理

### 3个阻塞队列
- **ArrayBlockingQueue**：有界队列(数组下标最大是int的最大值)，FIFO
- **LinkedBlockingQueue**：可设置容量队列；基于链表；FIFO；如果不设置容量，会是一个无边界的阻塞队列，最大长度为`Integer.MAX_VALUE`；
- **SynchronousQueue**：同步队列。容量为1;不会保存提交的任务，直接创建新线程来执行新的任务，每个put必须等待一个take


### 4种拒绝策略
当前线程数达到maxinumPoolSize时，新的任务会被采用拒绝策略处理
构造方法中handler参数是指定拒绝策略
![在这里插入图片描述](https://img-blog.csdnimg.cn/c19c337de3944842b8a125d821db3185.png)


```java
public class MyTask implements Runnable {

    private int taskNum;

    public MyTask(int num) {
        this.taskNum = num;
    }

    @Override
    public void run() {
        try {
            Thread.currentThread().sleep(4000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+":task "+taskNum+"执行完毕");
    }
}

public class Test {
	public static void main(String[] args) {
        //创建线程池                                 7
        ThreadPoolExecutor executor = new ThreadPoolExecutor(2,
                                          					 5, 
                                          					 200,
                                                         	 TimeUnit.MILLISECONDS,
                                                         	 new 	ArrayBlockingQueue<>(2),
                                                         	 Executors.defaultThreadFactory(),
                                                         	 new ThreadPoolExecutor.DiscardPolicy());
        for(int i=1;i<=8;i++){
            MyTask myTask = new MyTask(i);
            executor.execute(myTask);//添加任务到线程池
            //Future<?> submit = executor.submit(myTask);
        }
        executor.shutdown();
    }
}
```

- **AbortPolicy**：抛出异常
	![在这里插入图片描述](https://img-blog.csdnimg.cn/3e365fd9ad73442cb54f75071e7cfc9a.png)

- **CallerRunsPolicy**：线程池未关闭 ->  在调用者线程中运行当前任务(如任务在main线程中，就由main线程去执行)
	![在这里插入图片描述](https://img-blog.csdnimg.cn/1703e62e887b467ca9012cfad61b3255.png)

- **DiscardOlddestPolicy**：丢弃等待时间最长的任务

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/40025259cb58414e8e4b5f5bacb10099.png)


- **DiscardPolicy**：直接丢弃，不做处理
	![在这里插入图片描述](https://img-blog.csdnimg.cn/e8e42f2a643046a99ecf421596b81166.png)


### execute 与 submit区别

- **void execute(Runnable command)**：适合不关注返回值的场景
- **Future<?> submit(Runnable task)**：适合关注返回值的场景

### 如何关闭线程池
- **void shutdown()**：不再接受新任务，也不会停止正在执行的任务
- **List<Runnable> shutdownNow()**：调用了`interruptWorkers()`,暂停任务，取消等待的任务，返回没有执行的任务列表

### ctl属性解析

```java
//ctl是一个线程安全的Integer对象  
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
//Integer.SIZE=32;   COUNT_BITS=29
private static final int COUNT_BITS = Integer.SIZE - 3;
//工作线程(核心池线程 +非核心池线程)的最大数量
private static final int CAPACITY   = (1 << COUNT_BITS) - 1;
//1<<29:  00100000 00000000 00000000 00000000
//1<<29-1:00011111 11111111 11111111 11111111   工作线程的最大数量
```
- ctl表示了两个状态：
1. (**高3位**)表示线程池当前的**状态**
2. (**低29位**)表示线程池当前的**工作线程个数**

#### 线程池的5种状态

-1的二进制：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b2ae415a9b5241618049b9ae0b47af14.png)
32位也就是   `11111111 11111111 11111111 11111111`

```java
// runState is stored in the high-order bits 运行状态存储在高位，也就是高3位
//11111111 11111111 11111111 11111111 << 29  左移29位，后面补0
//11100000 00000000 00000000 00000000   就是RUNNING 即111
private static final int RUNNING    = -1 << COUNT_BITS; //111
private static final int SHUTDOWN   =  0 << COUNT_BITS; //000
private static final int STOP       =  1 << COUNT_BITS; //001
private static final int TIDYING    =  2 << COUNT_BITS; //010
private static final int TERMINATED =  3 << COUNT_BITS; //011
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/73691945816545f8b823dafafe173d4d.png)
#### runStateOf方法---获取线程池状态

```java
private static int runStateOf(int c)     { 
	//    ctl & 对capacity取非
	return c & ~CAPACITY; 
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b10eb0017f574c0d8ca369933895ce65.png)


#### workerCountOf方法---计算当前线程池中工作线程个数

```java
private static int workerCountOf ( int c){
	return c & CAPACITY;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/752ddd2f39114fd9ab7064b6e7f25cea.png)

#### ctlOf方法---打包ctl
runStateOf 和 workerCountOf方法都是拆包ctl的方法

```java
//根据runState和workerCount计算ctl
private static int ctlOf ( int rs, int wc){
	return rs | wc;
}
```






### execute方法解析
- excute方法做了一些避免并发情况下的判断(重新获取ctl)
- 添加一个空任务的非核心线程到线程池中(处理工作线程数已经为0，队列中还有任务排队的情况)
![在这里插入图片描述](https://img-blog.csdnimg.cn/022837c409f641c184c429fbd9bac994.png)



### addWorker方法解析
- 添加工作线程，并启动工作线程
```java
//添加工作线程
private boolean addWorker(Runnable firstTask, boolean core) {
	//对线程池状态及线程工作数量的判断
	retry://外层for的标识
	for (;;) {
		int c = ctl.get();//获取ctl的值
		int rs = runStateOf(c);//获取线程池状态
		
		// Check if queue empty only if necessary.仅在必要时检查队列是否为空
		//如果线程池状态不是RUNNGIN，查看当前任务是否可以不做处理
		if (rs >= SHUTDOWN &&
			//当前线程池状态是SHUTDOWN,且 任务为空，且工作队列有任务排队
			//同时满足这三个条件，就处理工作队列中的当前任务
			! (rs == SHUTDOWN && firstTask == null && ! workQueue.isEmpty()))
			//不是RUNNING状态就不处理新任务
			//如果是SHUTDOWN状态，且满足addWorker(null,false),且工作队列有任务，不进入if
			return false;
		
		for (;;) {
			int wc = workerCountOf(c);//获取当前工作线程数量
			//判断工作线程是否大于最大值，是就return false，不是继续判断|| 后面的
			if (wc >= CAPACITY ||
				//没有达到最大值继续判断
				//如果是核心线程，判断是否大于corePoolSize
				//如果是非核心线程，判断是否大于maximumPoolSize   大于就return false
				wc >= (core ? corePoolSize : maximumPoolSize))
				//当前工作线程已经达到最大值
				return false;
			//cas方式 对工作线程数+1，成功跳出外层for
			if (compareAndIncrementWorkerCount(c))
				break retry;
			//失败说明是并发情况，重新获取ctl的value
			c = ctl.get();  // Re-read ctl
			//获取线程池状态，如果与之前状态不一致，重新外层循环：判断线程池状态
			if (runStateOf(c) != rs)
				//并发操作导致线程状态变化了，需要重新判断状态
				continue retry;
			// else CAS failed due to workerCount change; retry inner loop
		}
	}
	//添加工作线程，并启动工作线程


	boolean workerStarted = false;//工作线程是否启动
	boolean workerAdded = false; //工作线程是否添加
	Worker w = null;//worker就是工作线程
	try {
		w = new Worker(firstTask);//将任务分给线程
		final Thread t = w.thread;//拿到worker绑定的线程
		if (t != null) {
			//加锁；防止在执行期间，被其他线程执行了shutdown()
			final ReentrantLock mainLock = this.mainLock;
			mainLock.lock();
			try {
				//基于重新获取的ctl，获取线程池的状态
				int rs = runStateOf(ctl.get());
				//如果满足线程池状态为RUNNING，就添加工作线程
				if (rs < SHUTDOWN ||
					//如果线程池状态为SHUTDOWN，且传入任务为null
					(rs == SHUTDOWN && firstTask == null)) {
					//开始添加工作线程
					//判断当前线程是是否存活
					if (t.isAlive()) // precheck that t is startable
						throw new IllegalThreadStateException();
					//将worker工作线程添加到HashSet中
					workers.add(w);
					//获取工作线程个数
					int s = workers.size();
					//当前工作线程数>历史最大工作线程数，更新largestPoolSize 
					if (s > largestPoolSize)
						largestPoolSize = s;
					//将工作线程添加的标识设为true
					workerAdded = true;
				}
			} finally {
				mainLock.unlock();
			}
			if (workerAdded) {
				//添加线程成功，就启动线程
				t.start();
				//将线程状态设为true
				workerStarted = true;
			}
		}
	} finally {
		//如果工作线程启动失败，判断是否成功创建了线程，成功就从workers集合中remove该线程，将工作线程数-1，尝试将状态改为TIDYING
		if (! workerStarted)
			addWorkerFailed(w);
	}
	return workerStarted;
}
```
### worker对象

```java
//中断线程不是立即让线程停,只是将Thread的中断标识设为true
//中断需要契机：线程阻塞等..
private final class Worker
	extends AbstractQueuedSynchronizer //继承AQS是为了线程中断
	implements Runnable                //存储需要执行的任务
{

	//工作线程的Thread对象，初始化时创建
	final Thread thread;
	//需要执行的任务
	Runnable firstTask;


	Worker(Runnable firstTask) {
		//刚初始化出的工作线程不允许被中断
		setState(-1);//
		//将任务给firstTast
		this.firstTask = firstTask;
		//创建Thread对象
		this.thread = getThreadFactory().newThread(this);
	}

	//调用t.start()时，执行run()
	public void run() {
		runWorker(this);
	}
}
```





