
# 对象头
![在这里插入图片描述](https://img-blog.csdnimg.cn/e093276b9e9f41d3a6332c8fe07dc1db.png)
# 对象的创建过程
1. jvm 遇到new指令，检查能否在常量池中定位到一个类的符号引用，检查这个符号引用代表的类是否已经经历类加载的过程，没有就执行类加载。

2. 类加载完成 可以确定对象所需内存大小，分配方式有**碰撞指针**和**空闲列表**两种方式

3. 内存分配完成后，jvm将除对象头之外的内存空间都初始化为零值，保证对象的字段可以不初始化直接使用。（对使用TLAB也一样）
4. 设置对象头中的信息
5. 执行`<init>()` --- 调用构造方法进行初始化
6. 将对象指向引用变量(`如果是new Object()就没有这一步`)


对于Object obj = new Object()

1. new-> 申请一块空间，将变量赋为默认值(半初始化)
8. 调用构造方法，对变量进行赋值(初始化)
9. 建立关联关系,将对象地址赋给引用变量obj

![在这里插入图片描述](https://img-blog.csdnimg.cn/ccfe65dfa54a417aad9a9499e533783d.png)
此处使用`jclasslib`插件查看字节码，安装好后在View中，选择Show Bytecode With Jclasslib
![Vie](https://img-blog.csdnimg.cn/b9b801db621d4f87b1b5c3ab9cb9421c.png)




# 对象在内存中如何存储的（对象与数组存储不同）

![在这里插入图片描述](https://img-blog.csdnimg.cn/6182ada4870147d1b1240a2d79543f8b.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/16236e2400e04f309190e5c2b49a5f93.png)
- 对象头：
	- Mark Word：存储对象自身的运行时数据
	- Class Pointer：通过这个指针确定对象是哪个类的实例
- 实例数据：存储该类以及父类的属性信息
- 对齐填充：由于虚拟机要求，**对象大小必须是8字节的整数倍**，**对象头所占字节已经被设计为8字节整数倍**，所以如果当前对象所占字节不是8字节整数倍，就要进行对齐填充，补充n个字节,使其变为8整数倍的字节，来满足设计的规范


在idea中查看一个对象在内存中的存储信息，依赖`jol`包：java object layout
注意依赖的版本和使用的JDK版本对应

```java
<dependency>
   <groupId>org.openjdk.jol</groupId>
   <artifactId>jol-core</artifactId>
   <version>0.8</version>
</dependency>
```

## 测试没有变量的对象
```java
public class Test2 {
    private static  class User{

    }

    public static void main(String[] args) throws InterruptedException  {
        User user = new User();
        //                            解析实例               转为可打印的
        System.out.println(ClassLayout.parseInstance(user).toPrintable());
    }
}
```
结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/904774234b8c486eaf892c6922b3d942.png)
## 测试有变量的对象

```java
public class Test2 {
    private static  class User{
        String name;
        int age;
    }

    public static void main(String[] args) throws InterruptedException  {
        User user = new User();
        //                            解析实例               转为可打印的
        System.out.println(ClassLayout.parseInstance(user).toPrintable());
    }
}
```
结果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/94aa006c796146d58f5dcd88c5e19f33.png)
这里String类型的age变量占4字节

<font color='red'>引用类型的变量默认开启压缩，压缩后是4字节

## 查看数组的存储

```java
public class Test2 {
    private static  class User{
        String name;
        int age;
    }

    public static void main(String[] args) {
       User[] users = new User[10];
       System.out.println(ClassLayout.parseInstance(users).toPrintable());
    }
}    
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2991e04f06ac4376860bae4eb5879b2d.png)


new 一个长度为10的数组，一个元素的引用占4字节，所有元素引用一共占40字节
Mark Word=8字节，Class Pointer=4字节，Array Length=4字节
8+4+4+40=56字节






# 将一个对象作为锁时，对象在内存中的布局发生了什么变化？

```java
public static void main(String[] args) {
    User user = new User();
    //作为锁前：打印对象信息
    System.out.println(ClassLayout.parseInstance(user).toPrintable());

	//对象作为锁时，打印对象信息
    synchronized(user){
        System.out.println(ClassLayout.parseInstance(user).toPrintable());
    }
	//释放锁后：打印对象信息
    System.out.println(ClassLayout.parseInstance(user).toPrintable());
}
```
结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/5eb621d10f694c7d83232b2dcb09dd8f.png)
在使用synchronized锁住一个对象时，这个对象的mark word部分会作一个记录



# 对象怎么定位？
通过栈上的reference来操作堆上的对象

栈上的引用变量，是由JVM来实现访问对象的方式的

- 句柄访问：可能划分一块内存作为句柄池，reference中存储的就是对象的句柄地址，句柄中包含了对象的实例数据、类型数据的地址信息
	`对象移动时，只改变句柄中的实例数据指针，ref本身不用改变`
- 直接指针访问：堆中的对象内存布局要考虑如何来放置类型数据的信息，reference中存储的是对象地址，一次就能访问到
	`节省了一次指针开销，速度更快`

# new的对象怎么分配？

1. 如果对象不可逃逸，且属于热点数据，会在**栈上分配**内存
栈上分配的对象可以不用经历垃圾回收，直接弹出
2. 如果占空间很大，直接在**老年代分配**
3. 多线程下同时创建对象，要保证线程安全：在Eden中为每个线程开辟很小的空间**TLAB**，只占1%;每个线程独立创建，互不干扰，TLAB用完后，再使用`CAS+失败重试`来保证更新操作的原子性
4. 在**Eden**分配


![在这里插入图片描述](https://img-blog.csdnimg.cn/8c1adbe8656b40c285f4a5716f2bfb43.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/91df9c4a632c4b30bb4541d1d42c5f70.png)



# Object obj = new Object() 在内存中占了多少字节？

- Mark Word ：8字节
- Class Pointer：4字节
- Instance Data：各属性所占字节总和
	基本类型：byte/boolean=1字节、short/char=2字节、int/float=4字节、double/long=8字节
	引用类型：4字节
- Padding：当前对象所占字节不是8字节的整数倍的话，就进行对齐，补够字节

对象所占字节=Mark Word + Class Pointer + Instance Data + Padding

# Class对象在堆还是方法区中？
- Class对象在堆中
- 存放在方法区中的是类的**元数据**(即类加载器从class文件中提取出来的类型信息、方法信息、字段信息等)。

