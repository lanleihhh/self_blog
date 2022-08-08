
> Java 泛型（generics）是 JDK 5 中引入的一个新特性.
>
> 泛型提供了==编译时类型安全检测机制==,好处是能够在编译的时候就检查类型安全. 
>
> 使用Object可以接收任意类型的参数,但是存在类型转换的问题.泛型可以解决它.

>
> 泛型的参数只能是引用类型(类)
>
> 泛型的类型参数可以有多个
>
> 没有定义具体类型,默认为Object

## 泛型回顾

在学习JavaSE中的集合Collection时,以及自定义数组/链表/二叉树的元素类型时使用过泛型

集合中

```java
//ArrayList源码  
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
 transient Object[] elementData; // Object类型数组,可以接收任何引用类型
```

```java
ArrayList<Integer> list1 = new ArrayList();
ArrayList<String> list2 = new ArrayList();
```

自定义类中

```java
public class MyselfArray<T> {
	//数组当前元素个数
    private int size;

    //泛型数组
    private T[] data;
    
    //构造方法
    
    //数组其他方法
}
```

泛型是为了**解决**Object接收任意类型存在==类型转换==的隐患.

## 泛型类

泛型类: **泛型类型用于类的定义中**

通过泛型可以完成对一组类的操作对外开放相同的接口

普通的泛型类

```java
public class Demo<T> {
    //name的类型为泛型,由外部指定
    private T name;

    public T getName() {
        return name;
    }

    public void setName(T name) {
        this.name = name;
    }
}
```

传入的实参类型需与泛型的参数类型相同

```java
Demo<Integer> demo = new Demo<Integer>(123456);
```

## 泛型类派生子类

Demo做为父类

```java
public class Demo<T> 
```

**若子类也是泛型类,子类和父类的泛型类型要一致**

```java
public class DemoChild<T> extends Demo<T> 
```

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-Itu3Lvq3-1646020549921)(C:%5CUsers%5Clanlei%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1646017344204.png)\]](https://img-blog.csdnimg.cn/3d5b2b83e4ac4b088e0eb58039a6d5c0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)


**若子类不是泛型类,父类要明确数据类型**

```java
public class Demo1Child extends Demo<String>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/358cef281790416aa1ad7d588278c79c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_19,color_FFFFFF,t_70,g_se,x_16)


## 泛型接口

泛型接口与泛型类的定义和使用基本相同.

定义一个泛型接口

```java
@FunctionalInterface//功能接口,只能有一个方法
public interface GenericInterface<T> {
    void show();
}
```

**泛型类实现泛型接口**,子类和接口的泛型类型要一致

```java
public class DemoChild<T> extends Demo<T> implements GenericInterface{
    @Override
    public void show() {

    }
}
```

**普通类实现泛型接口**,接口要明确泛型的类型

```java
public class Demo1Child extends Demo<String> implements GenericInterface<Integer>{
    @Override
    public void show() {
        
    }
}
```



## 通配符 , 上边界 ,下边界

### 泛型通配符

类型通配符一般使用  ==?==  代替具体的==类型实参==.

```java
//  ?  通配符  表示实际传入的参数可以是任意的
public static void test(Demo<?> demo){
    System.out.println(demo.toString());
}
```

```java
//  ?   可以传任意类型
Demo<String> demo = new Demo<>();
Demo<Integer> demo0 = new Demo<>();
test(demo);//String
test(demo0);//Integer
```

### 通配符上限

- **类 / 接口 <? extends 实参类型>**

  要求该泛型的类型,只能是**实参类型**或是参类型的**子类类型**

```java
	//<? extends 类型>  类型通配符上限
    //该泛型的类型只能是  目标类型  及目标类型的   子类类型
    public static void test1(Demo<? extends Number> demo){
        System.out.println(demo.toString());
    }
```

```java
    //上限为Number  可以传Number和子类  Integer Float...
    Demo<Number> demo1 = new Demo<>();
    test1(demo1);//上限
    Demo<Integer> demo2 = new Demo<>();
    test1(demo2);//上限
```

### 通配符下限

- 类 / 接口 <? super 实参类型>

  要求该泛型的类型,只能说是实参类型或实参类型的父类类型

```java
	//<? super 类型>    类型通配符下限
    ////该泛型的类型只能是  目标类型  及目标类型的   父类类型
    public static void test2(Demo<? super Integer> demo){
        System.out.println(demo.toString());
```

```java
	//下限为Integer 可以传Integer 及 父类Number 超类Object
    Demo<Integer> demo3 = new Demo<>();
    test2(demo3);
    Demo<Number> demo4 = new Demo<>();
    test2(demo4);
    Demo<Object> demo5 = new Demo<>();
    test2(demo5);
```

## 类型擦除

泛型是Java 1.5 引入的概念,在之前没有泛型.但是泛型代码却能够和之前版本代码兼容,因为**信息只存在于编译阶段**,在==进入JVM之前====,与泛型相关的的信息会被擦除掉==,称为**类型擦除**

```java
package com.ffyc.forword.generic;

import java.lang.reflect.Field;

public class Demo<T> {
    //name的类型为泛型,由外部指定
    private T name;

    public T getName() {
        return name;
    }

    public void setName(T name) {
        this.name = name;
    }

    /**
     * 类型擦除
     * 通过  反射 查看泛型类在   运行时  的状态信息
     */
    public static void main(String[] args) throws NoSuchFieldException {
        Demo<String> demo = new Demo<>();
        demo.setName("jim");
        Field field = demo.getClass().getDeclaredField("name");
        System.out.println(field.getName()+"  type:"+field.getType());
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/881d7810f3ec463c99a4fb106c48b2ac.png)
可以看到在运行时,泛型被转为了Object类型

