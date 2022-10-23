# staitc
## 1.static变量

- 静态变量在**内存**中只有==一个==拷贝,JVM只为其**分配一次内存**

- 当在方法或代码块中改变时,下次无论谁访问,值都已经改变

- 在加载类的过程中,完成内存分配,使用类名访问

- 实例变量,每创建一个示例,便会为实例变量分配一次内存,**多个**拷贝,**互不影响**

**适合使用static变量的环境:**

1. 方便访问变量

2. 在对象间共享值

```java
package Test;
public class staticTest {
    public static void main(String[] args) {
        //实例变量
        staticTest t=new staticTest();
        staticTest t1=new staticTest();
        t1.a=10;
        System.out.println(t.a);
        System.out.println(t1.a);//通过实例化的对象来访问每个对象的a
        //static变量
        //System.out.println(a);静态变量中不能访问非静态变量
        staticTest.b=10;
        System.out.println(staticTest.b);//通过类名来访问静态变量b

        //static 修饰的变量,只要被改变一次,所有的对象(或同所属类类)调用它的值都会发生改变,因为在内存中只有一份static变量
        System.out.println("原name:"+name);
        t.name="static";
        System.out.println("现name:"+name);
        System.out.println(t1.name);
        System.out.println(staticTest.name);
    }
    int a;
    static int b;
    static String name="静态";
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-sPnrQmW4-1618743974193)(img/p1.jpg)\]](https://img-blog.csdnimg.cn/20210418190716576.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 2.static方法

```java
package Test;
public class staticTest {
    public static void main(String[] args) {
        staticTest.a1();
        //staticTest.a2();
        /*
        报错:Non-static method 'a2()' cannot be referenced from a static context
        静态方法/代码块中不能调用非静态方法
        但非静态方法可以调用静态方法
        因为在类被加载时,静态方法就已经被加载好了,先于对象存在
        */
    }
    public static void a1(){
        System.out.println("类中的静态方法");
    }
    public void a2(){
        System.out.println("l类中的非静态方法");
        a1();//非静态方法调用静态方法可行
    }  
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-PEEF8daz-1618743974202)(img/p5.jpg)\]](https://img-blog.csdnimg.cn/20210418190730637.jpg#pic_center)




## 3.static代码块

```java
//代码块(代码块不限个数)   
//静态代码块:在类被加载的时候执行,只执行一次    
//static{任何符合java语法规则的代码块}    
//实例代码块:在创建对象的时候执行,创建n个执行n次    
//{任何符合java语法规则的代码块}
```

```java
package Test;
public class staticTest {
    public static void main(String[] args) {
        //主方法中没有任何操作
    }
    public static void a1(){
        System.out.println("类中的静态方法");
    }
    public void a2(){
        System.out.println("l类中的非静态方法");
        a1();
    }
    public staticTest(){//无参构造器
        System.out.println("无参构造器");
    }
    //实例代码块
    {
        System.out.println("实例代码块1");
    }
    {
        System.out.println("实例代码块2");
    }
    static{//静态代码块1
        System.out.println("静态代码块1");
    }
    static{//静态代码块2
        System.out.println("静态代码块2");
    }
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-msk7p0Eq-1618743974206)(img/p4.jpg)\]](https://img-blog.csdnimg.cn/20210418190759360.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


这里主方法中不设任何操作(创建对象或访问变量/方法),仍旧执行了static代码块中的代码,

任何被static修饰的都在加载类的同时加载,不需要靠对象来访问执行

```java
package Test;
public class staticTest {
    public static void main(String[] args) {
        new staticTest();
        staticTest s1=new staticTest();
        //创建两个对象,运行
    }
    public static void a1(){
        System.out.println("类中的静态方法");
    }
    public void a2(){
        System.out.println("l类中的非静态方法");
        a1();
    }
    public staticTest(){//无参构造器
        System.out.println("无参构造器");
    }
    //实例代码块
    {
        System.out.println("实例代码块1");
    }
    {
        System.out.println("实例代码块2");
    }
    static{//静态代码块1
        System.out.println("静态代码块1");
    }
    static{//静态代码块2
        System.out.println("静态代码块2");
    }
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-B7r3Bvyr-1618743974211)(img/p6.jpg)\]](https://img-blog.csdnimg.cn/20210418190811374.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


可以看到,创建了两个对象,除静态代码块外,都执行了两次.

**代码**顺序为:

构造器,实例代码块,静态代码块



**执行**顺序为:

**静态代码块**(类被加载时加载,先于对象),实例代码块(创建对象时被执行),构造器(对象创建完成后初始化赋值)

类的加载顺序

```java
java类的加载顺序:
1.静态变量/代码块/方法
2.实例代码块/方法
3.构造器
```

什么时候类被加载?

- 第一次创建对象(类/子类的实例化)
- 使用类中的静态变量/方法
- 定义了main方法的类,启动main方法时会加载该类

## 4.static 内部类
也称为嵌套内部类,不能访问外部类中非静态属性/方法

实例化:

外部类.内部类 对象名 = new 外部类.内部类();

```java
package java_gui.inner;
        //    外部类
public class Outter {

    private static String str1 = "静态变量";
    private  String str2 = "非静态变量";

    //静态内部类
    static class Inner2 {
        public void method() {
            System.out.println(str1);
            //System.out.println(str2);
            //静态内部类中不能访问外部类中非静态属性/方法
            //Non-static field 'str2' cannot be referenced from a static context
        }
    }
	
    public static void main(String[] args) {
        
        Outter.Inner2 inner2 = new Outter.Inner2();//静态内部类
        inner2.method();
        //结果:静态变量
    }
}

```
# final、finally、finalize
>- final:      修饰类(不能继承)、属性(不能修改)、方法(不能重写)
>- finally:   用于异常处理中try-catch-finally或try-finally。无论异常与否都会执行finally块中的代码。
>- finalize:方法，GC调用该方法进行对象的垃圾回收
## final
- final 最终的
- final修饰属性(一旦被赋值后就不能被改变)
	final修饰的属性可以在编译期间(**声明时直接赋值**)或运行期间(**构造方法**)对其初始化
- static final 修饰属性（常量）
	必须在声明时就初始化赋值，这个常量在内存中只有一份，被所有的对象共享
- 修饰方法(不能被重写)
- 修饰类(不能被继承)
### 使用final修饰一个变量时，是引用不能变，还是引用的对象不能变？
- 修饰基本类型时，它的值不能改变
- 修饰引用类型时，引用不能改变

### 什么是不可变类？
**不可变类**
- 一旦对象被创建，在对象存活期间，属性不能修改
- Java类库中的`String`,`八大基本类型包装类`，`BigInteger`,`BigDecimal`等都是不可变类
- 不可变类的对象可以最为另一个不可变类的属性

**设计一个不可变类需要遵循的规则**
1. fianl修饰类   or   final修饰所有成员方法or(java源码都是使用final修饰类)
2. 成员变量都用`private final`修饰
3. 不提供`setter`这种直接修改属性的方法
4. 通过构造方法初始化成员，拷贝对象，
	```java
	public final class MyImmutableDemo {  
	    private final int[] myArray;  
	    public MyImmutableDemo(int[] array) {  
	        this.myArray = array.clone();   
	    }   
	}
	```

6. `getter`方法中返回对象的克隆（防止通过getter获得内部可变对象后进行直接操作）
	

	```java
	public final class MyImmutableDemo {  
	    private final int[] myArray;  
	    public getMyArray(){
	    	int[] arr = array.clone();
	     	return arr;
	    }   
	}
	```
#### String中如何通过保护性拷贝来实现不可变？
来看String的两个方法

1. `substring(int beginIndex)`将字符串从指定索引处截取，返回截取的部分
```java
public String substring(int beginIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    int subLen = value.length - beginIndex;
    if (subLen < 0) {
        throw new StringIndexOutOfBoundsException(subLen);
    }
    //返回的String是new了一个新的，不是返回的原来对象
    return (beginIndex == 0) ? this : new String(value, beginIndex, subLen);
}
```

2. `String(char value[], int offset, int count) `
将传入的字符数组部分变成字符串，offset是起始位置，count是范围大小
```java
 public String(char value[], int offset, int count) {
     if (offset < 0) {
         throw new StringIndexOutOfBoundsException(offset);
     }
     if (count <= 0) {
         if (count < 0) {
             throw new StringIndexOutOfBoundsException(count);
         }
         if (offset <= value.length) {
             this.value = "".value;
             return;
         }
     }
     // Note: offset or count might be near -1>>>1.
     if (offset > value.length - count) {
         throw new StringIndexOutOfBoundsException(offset + count);
     }
     //该构造方法中，要对final修饰的value操作时，将传入的value数组拷贝一份赋给了源码中的value数组
     this.value = Arrays.copyOfRange(value, offset, offset+count);
 }
```
## finally
- 无论try中是否出现异常，finally块中的代码都会执行
- try和finally中都有返回值，会返回finally块中的
- try-catch-finally中，可以有多个catch(多个catch捕获的异常类型要从小往大写),只能有一个finally
## finalize
- `finalize()`方法是`Object`中的方法
- 当堆中的对象没有被栈中的变量引用指向时，对象会等待被GC回收
- 也可以重写该方法，自定义地释放资源（IO连接等）

# this 与 super
>this ：只能在方法中使用，表示调用该方法的对象的引用
>super：表示父类对象的引用

## this
- this.成员变量：调用本类的成员变量，也可调用父类的成员变量
- this.成员方法：调用本类的成员方法，也可调用父类的成员方法
- this.构造方法：调用本类的构造方法

## super
- super.成员变量：调用父类的成员变量
- super.成员方法：调用父类的成员方法
- super.构造方法：调用父类的构造方法

# instanceof
>测试 instanceof 左边的对象是否是右边的实例，是返回true，不是返回false

```java
	System.out.println(new String() instanceof Object);//true
    int[] arr = new int[10];
    System.out.println(arr instanceof int[]);//true
    String[] strings = new String[10];
    System.out.println(strings instanceof String[]);//true
```
- 基本类型不能使用instanceof判断
- 基本类型的数组、引用类型、引用类型的数组都可以判断
- `null`只能在instanceof的左边，null与任何引用类型进行instanceof运算都是false

## 使用场景
需要强制类型转换时，需要使用instanceof做判断

```java
public B convert(A a) {
    if (a instanceof B) {
        return (B) a;
    }
    return null;
}
```


# synchronized
>同步锁，保证被锁的内容同一时刻只会有一个线程来访问or执行

## 对象锁
### 修饰方法

1.**同步整个方法**

```java
public synchronized void copyInto(Object[] anArray) {
    System.arraycopy(elementData, 0, anArray, 0, elementCount);
}
```
2.**子类重写父类的同步方法时，若想要同步，必须声明为synchronized。**

```java
class Parent {
   public synchronized void method() { }
}
class Child extends Parent {
   public synchronized void method() { }
} 
```

3.**子类可以在想要同步的方法中调用父类的同步方法，来让该方法同步**

```java
class Parent {
   public synchronized void method() {   }
}
class Child extends Parent {
   public void method() { super.method();   }
} 
```
注意：
1. **接口中定义方法，不能使用synchronized关键字**
2. **构造方法不能使用synchronized关键字，可以在构造方法中使用同步代码块**
		
### 修饰代码块


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
            synchronized (this){
                if(num>0){
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()+"剩余票:"+num);
                    num--;
                }else{
                    break;
                }
            }//同步代码块,执行完成后,同步对象(锁)会自动释放
        }
    }
}
```
创建一个任务对象

```java
//创建一个任务对象
TicketRunnable ticketRunnable  = new TicketRunnable();

//创建两个线程,执行同一任务
Thread t1 = new Thread(ticketRunnable,"窗口1");
Thread t2 = new Thread(ticketRunnable,"窗口2");

t1.start();
t2.start();
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/bbcb411e2e614399b54d9da55be2f8eb.png)

**多个并发线程访问同一个对象中的同步代码块，同一时刻只能有一个线程去访问，其他线程阻塞，等待当前线程释放锁。**


```java
TicketRunnable r1  = new TicketRunnable();
TicketRunnable r2  = new TicketRunnable();

Thread t1 = new Thread(r1);
Thread t2 = new Thread(r2);

t1.start();
t2.start();
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4a29a4f69674b41b7bdf99d394643fd.png)

多个线程访问多个对象，线程同时在执行，因为synchronized锁的是对象，每个对象只有一个锁与之关联
## 类锁
### 修饰静态方法
静态方法属于类的方法，不属于对象的方法
synchronized修饰静态方法，锁的是这个类的所有对象
多个线程访问类的静态方法，多个线程使用同一把锁
```java
class CountRunnableTest implements Runnable {
    private static int count;

    public CountRunnableTest() {
        count = 0;
    }

    public synchronized static void method() {
        for (int i = 0; i < 5; i ++) {
            try {
                System.out.println(Thread.currentThread().getName() + ":" + (count++));
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public synchronized void run() {
        method();
    }
}

 class Test{

    public static void main(String args[]){
        CountRunnableTest runnableTest1 = new CountRunnableTest();
        CountRunnableTest runnableTest2 = new CountRunnableTest();

        Thread t1 = new Thread(runnableTest1, "线程1");
        Thread t2 = new Thread(runnableTest2, "线程2");
        t1.start();
        t2.start();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/e34aec5a6a914f009a992b62072b15fd.png)


### 作用在类上
与修饰static方法一样，都是所有对象共用一把锁
```java
 class ClassName{
 	 public void method() {
        synchronized (CountRunnableTest.class){
			//
        }
    }
 }
```

```java
class  CountRunnableTest implements Runnable {
    private static int count;

    public CountRunnableTest() {
        count = 0;
    }

    public synchronized static void method() {
        synchronized (CountRunnableTest.class){
            for (int i = 0; i < 5; i ++) {
                try {
                    System.out.println(Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public  void run() {
        method();
    }
	
	public static void main(String args[]){
        CountRunnableTest runnableTest1 = new CountRunnableTest();
        CountRunnableTest runnableTest2 = new CountRunnableTest();

        Thread t1 = new Thread(runnableTest1, "线程1");
        Thread t2 = new Thread(runnableTest2, "线程2");
        t1.start();
        t2.start();
    }
}
```

# transient 
>tansient：短暂的
被tansient修饰的变量不会被序列化

- 序列化就是将Java对象转为二进制字节存储到本地硬盘或数据库
将二进制字节转为Java对象就是反序列化
- 一个类的实例要想被序列化，必须实现Serializable接口

## serialVersionUID
当序列化时，将该类的serialVersionUID写入文件
反序列化时，检测文件中的serialVersionUID是否与当前类的serialVersionUID一致，若一致说明序列化对象与当前类对象版本一致，可以序列化。

### serialVersionUID有两种显示的生成方式：        
1. 默认的1L，比如：
`private static final long serialVersionUID = 1L; `       
3. 根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段，比如：        
`private static final  long   serialVersionUID = xxxxL;`


## 在idea中提示生成serialVersionUID
1. 进入Setting
2. 进入Editor
3. 进入Inspections
4. 搜索`UID`
5. 勾选`Serializable class without 'serialVersionUID'`->apply->ok
![在这里插入图片描述](https://img-blog.csdnimg.cn/8b65c65dd68145eeb4f5e58d11ba2afb.png)

```java
class User implements Serializable {
	
    private static final long serialVersionUID = 8294180014912103005L;
    private String username;
    private transient String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

```java
public static void main(String[] args) {
        User user = new User();
             user.setUsername("admin");
             user.setPassword("123456");
        System.out.println("read before Serializable: ");
        System.out.println("username:" + user.getUsername());
        System.err.println("password:" + user.getPassword());
        try {
            ObjectOutputStream os = new ObjectOutputStream(new FileOutputStream("E:\\user.txt"));
            os.writeObject(user); //将User对象写进文件
            os.flush();//刷新输出流，并强制写出缓冲的输出字节
            os.close();//关闭输出流
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            ObjectInputStream is = new ObjectInputStream(new FileInputStream("E:\\user.txt"));
            user = (User) is.readObject(); //从流中读取User的数据
            is.close();
            System.out.println("read after Serializable: ");
            System.out.println("username:" + user.getUsername());
            System.err.println("password:"+ user.getPassword());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/e4f12b239bfb4a97bbcd63f2d8897af9.png)
## 不能序列化成员方法or静态成员变量
序列化是保存对象的状态，而静态变量属于类的状态，不能被序列化保存

# default
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



