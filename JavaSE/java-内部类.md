
# 概念

把类定义在另一个类的内部，该类就被称为内部类。 

如果在类 Outer 的内部再定义一个类 Inner，此时类 Inner 就称为**内部类** （或称为嵌套类），而类 Outer 则称为**外部类**（或称为宿主类） 

根据内部类的位置，修饰符和定义方式可以分为**成员内部类**，**静态内部类**，**局部内部类**和**匿名内部类** 

内部类的访问

- 内部类可以直接访问外部类的属性与方法

- 外部类需要创建内部类的对象去访问其属性与方法

# 内部类的分类

- 成员内部类
- 局部内部类
- 静态内部类
- 匿名内部类

## 成员内部类

概念:位于外部类成员位置的类

特点:可以使用外部类中所有成员变量和成员方法(包括private修饰的)

实例化:**外部类.内部类 类变量 = new 外部类().new 内部类();**

示例:

```java
package java_gui.inner;
        //    外部类
public class Outter {
    private String name="外部类私有属性";
    
    //成员内部类
    class Inner1 {
        public void method() {
            //成员内部类可访问外部类属性与方法
            System.out.println(name);
        }
    }
  
}
```

测试:

```java
package java_gui.inner;
public class Test {
    public static void main(String[] args) {
        //外部类.内部类 类变量 = new 外部类().new 内部类();
        Outter.Inner1 inner1 = new Outter().new Inner1();
        inner1.method();
        //外部类私有属性
    }
}
```



**通过private修饰内部类**

使用private修饰内部类,就无法通过创建对象来轻易地访问内部类了,定义一个public修饰的方法,返回新的内部类对象,可以在方法中添加判断语句起到数据安全的作用

示例:

```java
package java_gui.inner;
        //    外部类
public class Outter {

    private String name="外部类私有属性";
    private static Inner1 inner1;


    //返回成员内部类对象
    public Inner1 getInner1(){
        if(inner1==null){
            inner1 = new Inner1();
        }
        return inner1;
    }

    //成员内部类
    private class Inner1 {
        public void method() {
            //成员内部类可访问外部类属性与方法
            System.out.println(name);
        }
        
        private void method1(){
            System.out.println("成员内部类方法");
        }
    }
    
     public static void main(String[] args) {
        Outter.Inner1 inner1 = new Outter().getInner1();
        inner1.method();
        inner1.method1();
         /*结果:
         外部类私有属性
         成员内部类方法
         */
         
    }
}
```

注意:

- 成员内部类中**不能写static**属性/方法
- 成员内部类可以看做外部类的成员,**先**创建**外**部类才能创建内部类
- 使用private修饰的成员内部类,只能在外部类的main()方法中访问



## 局部内部类

- 定义在方法或作用域中
- 只是作用域发生改变,只能在该方法和属性中被使用

- 与局部变量相同,不能被访问权限修饰符和static修饰符所修饰
- 不能定义static成员

```java
package java_gui.inner;
        //    外部类
public class Outter {

    public void test() {
        //若局部内部类访问局部变量,须将局部变量声明为final
        final String name="局部内部类";
        
        //局部内部类
        class Inner3 {
            public void method() {
				
                System.out.println(name);
            }
        }
        //局部内部类的实例化和使用必须在定义类的方法内
        new Inner3().method();
    }

    public static void main(String[] args) {
        new Outter().test();
        //结果:局部内部类
    }
}
```



## 静态内部类

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



## 匿名内部类

没有名字的类,是内部类的简化写法,适用于该内部类只使用一次的情况

声明:

```java
new 类/接口(){
//方法体
}
```

先定义一个接口

```java
public interface Inner4 {
    public abstract void method();
}
```

使用匿名内部类实现接口

```java
package java_gui.inner;
        //    外部类
public class Outter {

    public void test1 (){
        //匿名内部类  实现接口
        //写法1
        new Inner4(){
            @Override
            public void method() {
                System.out.println("匿名内部类");
            }
        }.method();
        
        /*//写法2
        Inner4 inner4 = new Inner4(){
            @Override
            public void method() {
                System.out.println("匿名内部类");
            }
        };
        inner4.method();
        */
    }
	
    public static void main(String[] args) {
       new Outter().test1();
        //结果:匿名内部类
    }
}
```

注意:

- 匿名内部类没有构造方法
- 匿名内部类中不能定义静态属性/方法/类
- 匿名内部类也是局部内部类的一种,局部内部类的注意事项对其也使用
- 内部类中使用this表示内部类对象,**外部类.this**表示外部类对象

# 意义

- 封装性



- 多继承(接口)



- 解决继承的类与实现接口方法重名

