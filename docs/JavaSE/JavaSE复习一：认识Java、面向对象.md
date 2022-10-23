# Java语言的特征？
1. 开源的
2. 面向对象(封装、继承、多态)
3. 跨平台运行(JVM来实现的)
    - 编写的java代码`hello.java`通过编译器编译为`hello.class`文件
    - `.class`字节码文件在不同平台(windows、linux、mac)通过JVM翻译成特定平台下的机器码指令，然后运行
5. 多线程(支持多个线程同时执行，由Thread类及其子类创建，线程执行的方法体写在`run()`中)
6. 安全性
7. 网络编程
8. 健壮性（强类型、异常处理、垃圾回收机制）

# Java如何实现跨平台运行？
1. 编写的代码`.java`文件通过编译器编译为`.class`字节码文件
2. 由JVM使用类加载器ClassLoader来加载`.class`文件
3. JVM解释器将`.class`文件翻译成对应平台的机器码，交给操作系统执行
![在这里插入图片描述](https://img-blog.csdnimg.cn/bd9733809cd245af937fe46a7252ef13.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/4cb225cb240249278922a9989c3cd586.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

# 面向对象

## 面向对象和面向过程的区别？
面向过程注重解决问题的步骤，实现一个功能需要什么样的流程，按照流程一步一步走。
面向对象注重谁去解决问题，将功能与谁去执行功能之间的关系抽象成了类，类的示例对象去执行某个方法(解决问题的函数，函数内部其实还是采用面向过程的思想)

|   区别   |                             POP                              |                             OOP                              |
| :------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 编程思想 |                一种以==过程==为中心的编程思想                | 一类以对象作为基本程序结构单位的程序设计语言，指用于描述的设计是以对象为核心，而对象是程序运行时刻的基本成分 |
|   特点   | 分析出解决问题所需要的==步骤==，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用 | 以==分类==的方式进行思考和解决问题。面向对象先对整体关系作出分类，然后根据不同的类深入细节的处理。面向对象的思想符合人类的认知习惯。 |
|   优势   | ==性能==比面向对象==高==（因为类调用时需要实例化，开销比较大，比较消耗资源） | ==易维护、易复用、易扩展==，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统 更加灵活、更加易于维护 |
|   缺点   |              没有面向对象易维护、易复用、易扩展              |                       性能比面向过程低                       |


## 面向对象的三大特征
### 封装
将类的信息隐藏在类的内部，对外不可见（外部程序不能直接访问），对外提供特定的访问方法

访问权限修饰符
- private   只能在本类访问
- default   在本类和同包类访问
- protected  在本类、同包类、不同包子类中访问
- public     任何类访问
#### 使用场景
1. private修饰属性，对外提供getter和setter方法来访问内部属性
2. private修饰构造方法，单例模式 ：提供一个getInstance()
    - 饿汉模式：new一个私有静态的对象，需要的时候直接通过getInstance访问
    - 懒汉模式：
       声明一个私有静态的对象，需要的时候通过getInstance()方法：
    	没有实例就new一个,有就直接返回
    	需要加synchronized同步锁，防止多线程同时进入，对象多次实例化

**饿汉单例**-----------直接创建，不管用不用

```java
public class SingleObject {
 
   //创建 SingleObject 的一个对象
   private static SingleObject instance = new SingleObject();
 
   //让构造函数为 private，这样该类就不会被实例化
   private SingleObject(){}
 
   //获取唯一可用的对象
   public static SingleObject getInstance(){
      return instance;
   }
}
```
**懒汉单例**-----------用时再创建（线程安全）

```java
public class Singleton {  
	//私有静态引用
    private static Singleton instance;  
    //私有构造器
    private Singleton (){}  
    //获取对象
    public static synchronized Singleton getInstance() {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        return instance;  
    }  
}
```

### 继承
- 子类继承父类的非私有的特征(属性)和行为(方法)，同时可以进行扩展，拥有自身的属性和方法
- java只支持单继承，支持多重继承（Map->AbstractMap->HashMap）

缺点：耦合性加强，独立性降低

#### extend 和 implements
一个类只可以extend一个类，但可以implements一个or多个接口
- 继承父类后可以使用父类的方法，也可以重写父类的方法
- 该类实现接口后，必须实现所有接口的抽象方法，

#### 什么情况不能使用继承？
1. 被final修饰的类
2. 类的构造方法是私有的

### 多态
同一事物在不同时刻体现出的不同状态就叫多态
多态需要的条件：
1. 有继承关系or实现关系
2. 子类重写了父类的方法
3. 父类的引用指向子类的对象


优点：
- 提高了程序的维护性(继承保证)
- 提升了程序的扩展性(多态保证)

缺点：
- 不能使用子类的特有功能，调用方法时
	1. 若父类有该方法，则去执行子类重写的方法
	2. 若父类没有该方法，则编译错误

注意
- 成员变量：编译看左边，运行看左边
- 静态方法：编译看左边，运行看左边
- 成员方法：编译看左边，**运行看右边**

## 重载与重写
**重载**：
- 发生在本类中。
- 两个或两个以上的同名方法
- 方法名相同
- 参数列表不同，返回值可以不同

**重写**：
- 发生在子类中。
- 当父类的方法不满足子类的需求时，子类可以重写父类的方法
- 重写的方法，方法名和参数列表必须和父类一致
- 重写方法的**返回值类型**必须**小于等于**父类方法的返回值类型
- 重写方法抛出的**异常类型**必须**小于等于**父类抛出的异常类型
- 重写方法的**权限**必须**大于等于**父类方法的权限

重写的变化
- Java1.5之前重写的方法返回值类型必须和父类一致；
- Java1.5之后可以是父类方法的返回类型or子类

==被final和static修饰的方法不能被重写==
```java
public class Demo {

    public Object test(){
        return new Object();
    }
}

public class Demo1 extends Demo{
    @Override
    public String test() {
        return "hello";
    }
}
```

## 抽象类和接口
抽象类
- 使用abstract修饰
- 对整个事物的行为、特征进行抽象
- 抽象类中有抽象方法(abstract修饰),抽象方法没有方法体,需要非抽象类的子类来重写
![在这里插入图片描述](https://img-blog.csdnimg.cn/43c87a2e96b544338aa9c0b8856861ea.png)
- 子类继承了抽象类,若不重写抽象方法,必须将该类也定义为抽象类
- 抽象类不能实例化
- 抽象类可以定义普通方法

接口
- interface
- 对类的行为进行抽象(方法)
- 接口中的属性默认都是`public static final`修饰的常量
- 接口中的方法默认都是`public abstract`修饰的抽象方法
- 接口中可以定义default修饰的包含方法体的默认方法(在扩展接口的同时,不会破坏已经实现的类)
- 接口可以继承多个接口，由实现类来实现所有方法

### 为什么接口中可以使用default方法？
Java8之前，在接口中只能声明抽象方法，不能声明有方法体的方法。

default方法是为了在原接口中**扩展**功能，但是对接口的**实现类不会造成影响**而出现的

例如Java源码中Iterable接口forEach和spliterator方法

```java
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```

```java
//spliterator既可以像Iterator那样逐个迭代，也可以批量迭代。
//批量迭代可以降低迭代的开销。
default Spliterator<T> spliterator() {
     return Spliterators.spliteratorUnknownSize(iterator(), 0);
}
```
### 抽象类有构造方法，为什么不能实例化？
Java规定了抽象类不能被实例化，设计抽象类就是为了继承![在这里插入图片描述](https://img-blog.csdnimg.cn/fe185b8d90334cbdb571c7d2380c1675.png)
- 抽象类的构造方法是为继承它的子类创建对象时，初始化父类成员使用的。如果实例化抽象类，它的抽象方法就无法调用。
- 抽象类只分配了在栈中的引用，没有分配堆中的内存
- 一个类定义为抽象类是从多个事物中提取出了共同的行为，但是行为的具体实现由具体的事物来决定，如果抽象类能够实例化，说明该类的行为是可以执行的，也就是该类不应该被定义为抽象类

