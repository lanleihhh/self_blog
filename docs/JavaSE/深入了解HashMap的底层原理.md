# JDK 1.7
## 核心属性
### 常量、变量

```java
    /**
     * 默认初始容量，必须是2的次方树     1<<4   左移四位，2^4=16
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
	
    /**
	 *	隐式指定了更大的容量，使用最大值：2^30
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;

    /**
     * 默认的负载因子 ，  可以在构造方法中指定负载因子
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * 空的存储Entry结点的数组
     */
    static final Entry<?,?>[] EMPTY_TABLE = {};

    /**
     * 真正存储数据的数组
     * table可根据需要调整大小，必须是2的次方
     */
    transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;

    /**
     * map中存储的的键值对个数，不是map的容量
     */
    transient int size;

    /**
     * 判断扩容的阈值 = 负载因子 * 容量
     * table是空数组EMPTY_TABLE时，阈值threshold = 容量capacity
     */
    int threshold;

    /**
     * 哈希表的负载因子，可以在构造方法中指定，不指定使用默认的0.75f
     */
    final float loadFactor;
```

### Entry结点---存储键值对的结构

```java
static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;
    int hash;

    /**
     * Creates new entry.
     */
    Entry(int h, K k, V v, Entry<K,V> n) {
        value = v;
        next = n;
        key = k;
        hash = h;
    }
}
```


## 构造方法
###  HashMap(int initialCapacity, float loadFactor)
创建一个指定数组容量，指定负载因子的HashMap
```java
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);

    this.loadFactor = loadFactor;
    threshold = initialCapacity;
    init();
}
```
### HashMap()
构造一个容量16，负载因子0.75 的HashMap
```java
public HashMap() {
	this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
}
```
### HashMap(int initialCapacity)
创建一个指定容量，默认负载因子（0.75）的HashMap
```java
public HashMap(int initialCapacity) {
	this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```


## put方法

```java
    /**
     * 添加指定的键值对key-value，若哈希表中已经有该key，将传入的value替换原来的value
     * @return 返回哈希表中key关联的上一个值，如果没有返回null
     */
    public V put(K key, V value) {
    	//判断哈希表是否为空，为空进行初始化
        if (table == EMPTY_TABLE) {
        	//初始化操作，第一次put时，threshold在构造方法中赋为16也就是默认的容量大小
            inflateTable(threshold);
        }
        /**----------key为空时的操作--------------*/
        if (key == null)
            return putForNullKey(value);
        /**----------key不为空时的操作--------------*/
        //计算哈希值
        int hash = hash(key);
        //哈希值和数组长度运算得到要插入的索引
        int i = indexFor(hash, table.length);
        /**----要插入的位置不为空时的操作   遍历链表，找到插入位置-------*/
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            //发生哈希冲突时，先比较哈希值
            //比较key是否是同一个对象，若是同一个对象，不比较equals
            //进入if条件  hash值相同 && 是同一个对象  
            //      or  hash值相同 && key的内容一样
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;//保存旧value
                e.value = value;//更新value
                e.recordAccess(this);
                return oldValue;//返回旧valule
            }
        }
        /**----------要插入的位置为空时的操作--------------*/
        modCount++;
        addEntry(hash, key, value, i);//添加结点
        return null;
    }
```

### 1. 判断数组，为空进行初始化

```java
//传入的threshold阈值，table为空时threshold是默认容量
private void inflateTable(int toSize) {
    // Find a power of 2 >= toSize
    //找一个 >= 参数的  2的次方数  
    //如：roundUpToPowerOf2(15)=16  roundUpToPowerOf2(30)=32
    int capacity = roundUpToPowerOf2(toSize);
	
	//计算新的阈值  阈值=容量*负载因子
    threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
    table = new Entry[capacity];//初始化table数组
    initHashSeedAsNeeded(capacity);
}
```

### 2.key为空时的操作:将key为null的键添加到索引0处的链表中

```java
/**
 * Offloaded version of put for null keys
 */
private V putForNullKey(V value) {
    for (Entry<K,V> e = table[0]; e != null; e = e.next) {
        if (e.key == null) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }
    modCount++;
    //hash=0 key=null value=value next=0
    addEntry(0, null, value, 0);
    return null;
}
```

### 3. 计算哈希值

```java
 /**
  * 
  * Note: Null keys always map to hash 0, thus index 0.空键映射到哈希0，空键的插入索引为0
  */
 final int hash(Object k) {
     int h = hashSeed;
     if (0 != h && k instanceof String) {
         return sun.misc.Hashing.stringHash32((String) k);
     }
	 //二次散列、解决哈希冲突；不直接用hashCode()
     h ^= k.hashCode();

     // This function ensures that hashCodes that differ only by
     // constant multiples at each bit position have a bounded
     // number of collisions (approximately 8 at default load factor).
     //扰动函数：核心思想，增加哈希值的不确定性，让元素分布更散列
     h ^= (h >>> 20) ^ (h >>> 12);
     return h ^ (h >>> 7) ^ (h >>> 4);
 }
```

### 4. 计算要插入的位置
用算出 `哈希值` 和 `数组长度-1`进行位运算
```java
static int indexFor(int h, int length) {
   // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
   return h & (length-1); // 和 (h & length) 结果相同,位运算效率更高
}
```
### 5. 要插入的位置有元素，遍历链表

```java
/**----要插入的位置不为空时的操作   遍历链表，找到插入位置-------*/
for (Entry<K,V> e = table[i]; e != null; e = e.next) {
    Object k;
    //发生哈希冲突时，先比较哈希值
    //比较key是否是同一个对象，若是同一个对象，不比较equals
    //进入if条件  hash值相同 && 是同一个对象  
    //      or  hash值相同 && key的内容一样
    if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
        V oldValue = e.value;//保存旧value
        e.value = value;//更新value
        e.recordAccess(this);
        return oldValue;//返回旧valule
    }
}
```

### 6. 要插入的位置没有元素，添加Entry结点（含扩容resize方法）

```java
/**
 * 新建一个封装了k,v,h,next的Entry对象，添加到桶中
 * 必要的时候进行扩容（元素个数 >= 容量 * 负载因子） 且 该要插入的为不为空
 * 子类可以重写put方法
 */
void addEntry(int hash, K key, V value, int bucketIndex) {
	//当元素个数 >= 容量 * 负载因子且待插入的位置不为空，进行扩容
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = indexFor(hash, table.length);
    }
	//创建Entry对象
    createEntry(hash, key, value, bucketIndex);
}

//创建Entry结点
void createEntry(int hash, K key, V value, int bucketIndex) {
	//将带插入位置的元素给e，也就是获取该位置的链表头结点
    Entry<K,V> e = table[bucketIndex];
    //创建Entry结点，放到待插入位置
    //   头插法  插入的结点的next指向原来的头结点
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```
### 7.当元素个数 >= 容量 * 负载因子且待插入的位置不为空，进行扩容

```java
void resize(int newCapacity) {
	//拿到原来的数组
    Entry[] oldTable = table;
    //拿到原来的容量
    int oldCapacity = oldTable.length;
    //旧容量 == 最大容量时...
    if (oldCapacity == MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return;
    }
	//新建一个2倍旧容量的数组
    Entry[] newTable = new Entry[newCapacity];
    //复制数组的操作
    transfer(newTable, initHashSeedAsNeeded(newCapacity));
    //将新的数组引用指向旧数组
    table = newTable;
    //更新扩容阈值
    threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
}
```
# JDK 1.8
## 核心属性

```java
//默认容量
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
//最大容量
static final int MAXIMUM_CAPACITY = 1 << 30;
//负载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;
//树化阈值   table的node中 链表长度 > TREEIFY_THRESHOLD 时转为红黑树
static final int TREEIFY_THRESHOLD = 8;
//反树化阈值  table的node中 红黑树长度为 < UNTREEIFY_THRESHOLD 时转为链表
static final int UNTREEIFY_THRESHOLD = 6;
//最小树化阈值  当node的个数 > 64 且 有node的链表长度 > 8 时，进行链表转红黑树
static final int MIN_TREEIFY_CAPACITY = 64;
```

## put方法

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
   Node<K,V>[] tab; Node<K,V> p; int n, i;
   //table为空进行初始化
   if ((tab = table) == null || (n = tab.length) == 0)
       n = (tab = resize()).length;//resize()方法包括了初始化和扩容
   //i是hash算出的要插入的位置结点，看要插入的位置是否为空
   if ((p = tab[i = (n - 1) & hash]) == null)
   		//为空，新建一个node插入该位置
       tab[i] = newNode(hash, key, value, null);
   else {//要插入的位置不为空  判断是链表还是树结构
       Node<K,V> e; K k;
       //要添加的key与原来的key相同
       if (p.hash == hash &&
           ((k = p.key) == key || (key != null && key.equals(k))))
           e = p;
       //如果当前节点是树结点，将k-v添加到红黑树中去     
       else if (p instanceof TreeNode)
           e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
       else {//不然就是链表，添加到链表的尾部	
           for (int binCount = 0; ; ++binCount) {//binCount统计链表的长度
               if ((e = p.next) == null) {//遍历到链表尾部
                   p.next = newNode(hash, key, value, null);//将结点插入表尾
                   //如果链表长度 >= 树化阈值  进行链表转红黑树
                   if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                       treeifyBin(tab, hash);
                   break;
               }
               //如果链表当前结点的key和要插入的key相同，直接退出
               if (e.hash == hash &&
                   ((k = e.key) == key || (key != null && key.equals(k))))
                   break;
               p = e;
           }
       }
       //添加的key与当前节点的key相同时，拿到旧的value并返回
       if (e != null) { // existing mapping for key
           V oldValue = e.value;
           if (!onlyIfAbsent || oldValue == null)
               e.value = value;
           afterNodeAccess(e);
           return oldValue;
       }
   }
   ++modCount;
   //数组结点个数 > 扩容阈值时，进行扩容操作
   if (++size > threshold)
       resize();
   afterNodeInsertion(evict);
   return null;
}
```



# jdk 1.8 中 HashMap的改动

1. hash算法简化（1.8使用了红黑树，1.7是通过复杂的hash让元素分布更散列，都是为了提高查询效率）
2. 1.7使用数组+链表作为数据结构，1.8使用数组+链表+红黑树
3. 1.7在链表插入使用头插法（多线程容易产生线程死锁）1.8使用尾插法（避免了死锁）



