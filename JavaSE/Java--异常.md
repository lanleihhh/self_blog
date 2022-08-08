# Exception
## 1. 概述

 在Java语言中，将程序执行中发生的不正常情况称为“异常” 

 Java程序在执行过程中所发生的异常(运行时一切不正常情况)事件可分 为两类 

Error 和 Exception

|          | Error错误                            | Excepiton异常                            |
| -------- | ------------------------------------ | ---------------------------------------- |
| 原因     | Java虚拟机无法解决的严重问题         | 编程错误or偶然的外在因素导致的一般性问题 |
| 实例     | JVM系统内部错误、资 源耗尽等严重情况 | 数组下标越界 算数规则不合法 网络连接中断 |
| 如何解决 | 一般不编写针对性的代码进行处理。     | 使用针对性的代码进行处理(try-catch)      |

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225009455.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




## 2. 异常体系

- Error表示错误(编译期错误/系统错误),程序无法处理的问题

- Exception表示异常,是所有异常的父类,运行时由于程序缺陷导致在运行过程中出现不正常情况,程序中应尽可能去处理这些异常

运行期异常( RuntimeException ):包括RuntimeException类异常与其子类异常(空指针,数组下标越界...)

编译期异常( Checked Exception ):除运行期异常以外的都是编译期异常(IO异常,SQL异常),语法上来说,是必须要进行**处理**(向上throw或try-catch)的异常,若不处理,程序就 不能编译通过



![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071222503685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 3. 常见异常

```java
//StackOverflowError  栈满溢出错误
    public static int test1(int num){
        return num+test1(num-1);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225044979.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




```java
 //ArrayIndexOutOfBoundsException   数组索引越界异常( <0 或 >=length )
    public static int test3(){
        int [] a={1,2,3,4,5};
        return a[5];
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225055485.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


```java
 public static String test4(){
        Object obj = new int[5];//多态
        String str = (String)obj;//向下转型
        return str;
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225104912.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


```java
public static int test5(){
        return 10/0;// by zero
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225113148.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


```java
// NullPointerException  空指针异常
    public static int test6(){
        String string =null;
        return string.length();
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071222512377.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)








## 4. 异常处理

 Java编程语言使用异常处理机制为程序提供了错误处理的能力 

 Java的异常处理是通过5个关键字来实现的：try、catch、 finally、 throw、throws 

 基本语法 try{ 

可能会发生异常的代码

 }catch(异常类型 引用名){ 

异常处理代码 }

finally{ 

必须执行代码 } 

- try

 检测不安全的代码块（发现异常） try块中任何一条语句发生了异常，下面的代码将不会被执行，程序将跳转到异常处理代码块中，即catch块。因此，不要随意将不相关的代码放到try块中，因为随时可能会中断执行 

- catch

 把抓到的类型匹配的异常捕获，保证程序能继续运行下去 catch语句必须紧跟着try语句之后，称为捕获异常，也就是异常处理函数， 一个try后面可以写多个catch，分别捕获不同类型的异常，要从子类往父类 的顺序写，否则有编译错误 

```java
/*
	try-catch的嵌套使用
*/
try {
            int a=10,
                b;
            System.out.println(a/b);//当b=0时,到此处终止,抛出异常,否则向下走
            try{
                String str = null;
                System.out.println(str.length());//字符串为空,空指针
            }catch (NullPointerException e){
                System.out.println("空指针");
                e.printStackTrace();
            }
        }catch (ArithmeticException e){
            e.printStackTrace();
            System.out.println("算数异常");
        }
```

```java
try {
            int a=10, b=10;
            System.out.println(a/b);
            String str = null;
            System.out.println(str.length());
        }
        catch (NullPointerException e){//捕获与本catch类型相同的异常
            System.out.println("空指针");
            e.printStackTrace();
        } catch (ArithmeticException e){
            e.printStackTrace();
            System.out.println("算数异常");
        }catch (Exception e){//大的异常类型,必须写在子类类型的下边,否则符合此类型及子类类型的异常都会去匹配它,导致后面的catch编译不通过
            System.out.println("出错!");
        }
```



- fianlly

无论是否抛出或捕获异常,finally块都会执行

注意:对一段代码进行异常处理时,try-catch可以有多个,**finally**只能有**一个**

```java
public class ExceptionDemo1 {
    public static void main(String[] args) {
        System.out.println(test1());
    } 
//try-catch-finally
    public static int  test1(){
        try {
            int a=10;
            return a/0;
        }catch (ArithmeticException e){
            e.printStackTrace();//打印异常信息
            //现实中使用一些日志组件,将信息输出到日志文件中
            System.out.println("算数异常");
            return -1;//因为还有fiannly,先不返回
        }finally{
            System.out.println("异常与否都执行");
            return 0;//返回并结束程序
        }
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225148929.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


```java
public class ExceptionDemo1 {
    public static void main(String[] args) {
        System.out.println(test2());
    } 

public static int test2(){
        try {
            int a=10, b=0;
            return a/b;
        }finally{//只有try-finally时,先执行完finally
            System.out.println("异常与否都执行");
            return -1;
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225156974.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


- throws  

  用在方法声明处,表示此方法不处理异常,交由此方法的调用处进行处理

  语法:[访问权限修饰符] 返回值 方法名 throws 异常类1,异常类2,异常类3...{...}

   抽象方法也可使用throws声明异常类型

  子类重写父类方法,子类方法不能声明抛出比父类方法类型更大的异常(针对编译期异常)

 使用了throws的方法,调用时必须处理声明的异常,要么使用try-catch,要么继续使用throws向上抛

```java
import java.io.UnsupportedEncodingException;
import java.text.ParseException;
import java.text.SimpleDateFormat;
/*
    使用了throws的方法,调用时要么用try-catch进行处理声明的异常,要么继续用throws声明
    1 主程序中调用带有throw的test1(),使用了try-catch
    2 test1调用test2,test2调用test3,都继续使用了throw声明
 */
public class ExceptionDemo2 {
    public static void main(String[] args) {
        try {
            new ExceptionDemo2().test3();
        }catch (UnsupportedEncodingException e){
            System.out.println("字符编码不支持");
        } catch (ParseException e) {
            System.out.println("解析有问题");
        }catch (NullPointerException e){
            System.out.println("空指针");
            e.printStackTrace();
        }finally {
            System.out.println("finally!");
        }
    }

    public void test1() throws UnsupportedEncodingException, ParseException {
        test2();
    }

    public void test2() throws UnsupportedEncodingException, ParseException {
        test3();
    }
    /*
    throws后一般声明的都是编译期异常
    NullPointerException           运行时异常
    UnsupportedEncodingException   编译期异常
     */
    public void test3() throws NullPointerException, UnsupportedEncodingException, ParseException {
        String str = "hello world";
        String s=null;
        str.getBytes("GBK");
        //System.out.println(s.length());
        SimpleDateFormat sdf = new SimpleDateFormat();
        sdf.parse("");
    }
}
```

```java
import java.io.UnsupportedEncodingException;
import java.text.ParseException;
import java.text.SimpleDateFormat;

public class ExceptionDemo3 extends ExceptionDemo2{

    /*
    子类重写父类方法,
    访问权限修饰符必须大于等于父类
    子类方法不能抛出比父类异常类型更大的异常(针对编译期异常)
     */
    @Override
    public void test3() throws UnsupportedEncodingException, ParseException {
        super.test3();
        String str = "hello world";
        System.out.println(str.charAt(str.length()));
    }
}
```

```java
public abstract class ExceptionDemo5 {

    /*
    抽象方法也能使用throws声明异常
    在继承抽象类,使用抽象方法时,要么用try-catch捕获异常,要么继续用throws声明
     */
    abstract void test() throws Exception;

}
```



```java
public class ExceptionDemo4 {
    public static void main(String[] args) {
        System.out.println(subFileName(null));
    }

    /*
    截取文件名
    */
    public static String subFileName(String fileName){
        if(fileName==null){
            throw new NullPointerException("文件名为空了");
        }else{
            //substring从指定索引开始截取,lastIndexOf最后一次出现改字符串/字符的索引
            return fileName.substring(fileName.lastIndexOf(".")+1);//只要"."后面的数据,索引+1
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071222521225.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)










- throw

  用在方法体中,显示地抛出异常,抛出一个异常类的实例化对象

  语法: throw  new  异常类构造方法  

  可以自定义错误提示内容  throw new RuntimeException("出错了");

  使用throw后,要么使用try-catch捕获异常,要么使用throws声明异常



```java
public static void test() throws NullPointerException{
        try {
            System.out.println(new String((byte[]) null));
        }catch(NullPointerException e){
            throw new NullPointerException("空指针");
            //e.printStackTrace();//打印异常信息
        }finally {
            System.out.println("结束");
        }

        /*String str=null;
        if(str==null){
            throw new NullPointerException("空指针");
        }*/
    }
```



自定义异常类

-  自定义异常类中往往不写其他方法，只重载需要使用的构造方法 
-  继承Exception,在方法中使用throw抛出后,必须在方法中try-catch或 throws抛出 

基本语法:

```java
public class 异常类名 extends Exception/RuntimeException{
	public 异常类名(String msg){
		super(msg);
	}
}
```

自定义分数异常类

```java
package util;
    /*
    自定义一个异常类
 */
  public  class ScoreException extends Exception{

        public ScoreException() {
            super();
        }

        public ScoreException(String message) {
            super(message);
        }
    }
```

测试

```java
package day2;

import util.ScoreException;

public class ScoreExceptionTest {
    public static void main(String[] args) throws ScoreException {
        System.out.println(score(600));
    }
    /*
    throws :声明异常类,表明此方法不处理异常,当调用此方法时,必须处理这些异常
            使用了throws的方法,调用时要么用try-catch进行处理声明的异常,要么继续用throws声明
            可以同时声明多个异常类
            抽象方法也可用throws声明异常类型

    throw  :显示抛出新的异常类的实例化对象
            throw new 异常类名(message);底层调用Throwable的构造方法
     */
    public static char score(double d) throws ScoreException {
        if(d>100||d<0){
            throw new ScoreException("分数不合法");
        }else if(d>=90){
            return 'A';
        }else if(d>=75&&d<90){
            return 'B';
        }else if(d>=60&&d<75){
            return 'C';
        }else {
            return 'D';
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210712225226465.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


























































































