
## ReentrantLock锁实现

```java
public class ReentrantLock implements Lock, java.io.Serializable
```

核心结构--3个内部类

```java
//继承AQS的同步类
abstract static class Sync extends AbstractQueuedSynchronizer{}
//公平锁
static final class FairSync extends Sync {}
//非公平锁
static final class NonfairSync extends Sync {}
```
lock()方法

```java
public void lock() {
	//调用内部类Sync的lock()方法，
   sync.lock();
}
```

# synchronized锁实现

## synchronized几种锁状态
1. **无锁**
2. **偏向锁**：只有一个线程访问，该线程自动获取锁
3. **轻量级锁**：锁状态为偏向锁时，又有线程访问，锁升级为轻量级锁，其他线程会自旋尝试获取锁(自旋到一定次数就阻塞了)，不会阻塞线程，提升了效率
4. **重量级锁**：
   锁状态为轻量级锁时，其他线程自旋到一定次数，线程阻塞，锁升级为重量级锁
   获取不到锁的线程将阻塞，等待操作系统调度