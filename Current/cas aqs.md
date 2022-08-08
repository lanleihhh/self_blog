# CAS
`Compare-And-Swap`比较并交换，硬件对并发的支持

>自旋：不断的循环尝试，对cas这里极速不断尝试判断内存值是否已经被更新过

CAS 是一种乐观锁(不加锁)，采用自旋的思想，轻量级锁。不会阻塞线程


- 内存值 V
  第一次从内存中读到的值
- 预估值 A
  (对读到的值进行操作，准备将操作结果写入前) 读到的内存值
- 更新值 B
  操作后的值

比较第一次读入的值和写入前读入的值是否相等，相等就 V = B
不相等，就继续循环，不会阻塞线程

## CAS 的 ABA问题
1. 一个线程读到的内存值为A
2. 在写入前，被其他线程将A修改为了B，又将B修改为了A
3. 此时该线程要将结果写入内存值，第二次读入的值也是A，无法确定是否被其他线程修改过

```java
public static void main(String[] args) throws InterruptedException {
    AtomicInteger atomicInteger = new AtomicInteger(100);//默认值为100
    new Thread(() -> {
        System.out.println(atomicInteger.compareAndSet(100, 101));//设置预期值是100  修改值为101   true
        System.out.println(atomicInteger.get());// 101   
        System.out.println(atomicInteger.compareAndSet(101, 100));;//设置预期值是101  修改值为100   true
        System.out.println(atomicInteger.get());//  100
    }).start();
    Thread.sleep(1000);
    new Thread(() -> {
        System.out.println(atomicInteger.compareAndSet(100, 101));//返回true 说明修改成功  发生ABA问题  true
        System.out.println(atomicInteger.get());// 101
    }).start();
}
```

## ABA 解决
给当前使用的类添加版本号
1. 读入(A,1）
2. 其他线程：(A,1)->(B,2); (B,2)->A(A,3)
3. 写入前读入的(A，3)与内存中(A,1)不相同，不执行更新

```java
public static void main(String[] args) throws InterruptedException {
	AtomicStampedReference stampedReference = new AtomicStampedReference(100, 0);
	new Thread(() -> {
	   try {
	       Thread.sleep(50);
	   } catch (InterruptedException e) {
	       e.printStackTrace();
	   }
	   stampedReference.compareAndSet(100,101,stampedReference.getStamp(),stampedReference.getStamp() + 1);
	   stampedReference.compareAndSet(101,100,stampedReference.getStamp(), stampedReference.getStamp() + 1);
	}).start();
	
	new Thread(() -> {
	   int stamp = stampedReference.getStamp();
	   try {
	       Thread.sleep(2000);
	   } catch (InterruptedException e) {
	       e.printStackTrace();
	   }
	   boolean result = stampedReference.compareAndSet(100, 101, stamp, stamp + 1);
	   System.out.println(String.format("  >>> 修改 stampedReference :: %s ", result));
	}).start();
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/40acbce3d34c4e819d15e627bf2b100d.png)
# AQS
在`java.util.concurrent.locks`包下
AQS -- `AbstractQueuedSynchronizer`抽象同步队列
AQS是JUC中实现线程安全的一个核心组件

- 内部维护了一个锁状态：volatile 修饰(保证了可见性和有序性)的state，初始为0
- 提供了对state的原子操作方法，保证了原子性
- 维护了一个队列，保存等待获取锁的线程
- 维护了一些获取锁、添加线程到等待队列、释放锁的方法


多个线程来访问，如果有一个线程访问到了state，将state + 1
其他线程不能访问了，加到等待队列中
该线程结束后，state变回0，队列中下一个等待获取锁的线程才能进来
