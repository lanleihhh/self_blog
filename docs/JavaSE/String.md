# String

## 1、声明

```java
//声明方式
String s1 = "hello";

String s2 = new String("hello");
```

```java
String s1="abc";
String s2="abc";
/*
首先会在字符串常量池中查找,若没有,就在常量池中创建
如果常量池中已经存在,就不需要创建,直接返回地址
*/
System.out.println(s1==s2);//true
System.out.println(s1.equals(s2));//true
```

**==  和  equals  的区别**

==       :  比较两个变量的地址是否相等

equals():  重写了Object类的equals()，比较两变量的内容（this.value）是否相等

```java
String str1=new String("abcd");
String str2=new String("abcd");//虽然内容相同,并不是同一个对象
String str3=str1;//两变量表示同一个对象，指向同一个地址
System.out.println(str1==str2);//false
System.out.println(str1==str3);//true
System.out.println(str1.equals(str2));//true   equals比较对象的内容
```

## 2、使用String的注意事项

```java
/*
所有字符串常量都是String类的实例(对象)
字符串不能更改,(一旦给定字符串的值,值就不能改变)因为底层用到一个final修饰的char数组
private final char value[];  底层是单个字符存储   char a=97 b=98 c=99
*/
```

```java
String s="hello";
s+=" world";
s+=" !";

String s1=new String("HELLO");
s1+=" WORLD";
s1+=" !";//当更改字符串的值时,将会创建一个新的字符串对象 (存储更改后的值)
System.out.println(s);
System.out.println(s1);
```

输出：

hello world !
HELLO WORLD !

## 3、String的常用方法
构造方法   | 
-------- | 
public String() | 
public String(String str) | 
public String(byte[] bytes)| 
public String(char[] value)|


| String的判断功能     | 作用                                  |
| -------------------- | ------------------------------------- |
| `equals()`           | 判断两个对象内容是否相等              |
| `equalsIgnoreCase()` | 判断两个对象内容是否相等,不区分大小写 |
| `contains()`         | 判断是否包含指定字符串                |
| `isEmpty()`          | 判断字符串长度是否为0                 |
| `startswith()`       | 判断是否以指定的字符串开头            |
| `endswith()`         | 判断是否以指定的字符串结尾            |

```java
String s1=new String("hello");
String s2="hello";
String s3=new String("HELLO");
String s4="";

System.out.println(s1.equals(s2));//判断两个对象内容是否相等
System.out.println(s1.equalsIgnoreCase(s3));//判断两个对象内容是否相等,不区分大小写
System.out.println(s1.contains("lo"));//判断是否包含指定字符串
System.out.println(s4.isEmpty());//判断字符串长度是否为0
System.out.println(s1.startsWith("he"));//判断是否以指定的字符串开头
System.out.println(s3.endsWith("LO"));//判断是否以指定的字符串结尾
```

运行结果:

```java
true
true
true
true
true
true
```

|            String获取功能             |                             作用                             |
| :-----------------------------------: | :----------------------------------------------------------: |
|            `int length()`             |          返回字符串长度,字符串底层用char型数组存储           |
|       `char charAt(int index)`        |                      返回指定索引的字符                      |
|         `int indexOf(int ch)`         |                  返回指定字符首次出现的位置                  |
| `int indexOf(int ch, int fromIndex)`  |          从指定索引开始,返回指定字符首次出现的位置           |
|       `int lastIndexOf(int ch)`       | 返回指定字符在字符串中最后一次出现处的位置，如果此字符串中没有这样的字符，则返回 -1 |
|     `String substring(int start)`     |        截取指定区间内字符串,   从开始索引~s.length-1         |
| `String substring(int start,int end)` |         截取指定区间内字符串, 从开始索引~结束索引-1          |



```java
String s1="hello world";

System.out.println(s1.length());//11
System.out.println(s1.indexOf('o'));//4
System.out.println(s1.indexOf('o',5));//7
System.out.println(s1.charAt(0));//h
System.out.println(s1.lastIndexOf('l'));//9


String s2=s1.substring(6);
String s3=s1.substring(3,9);
System.out.println(s2);//world
System.out.println(s3);//lo wor
```

|           String的转换功能            |                             作用                             |
| :-----------------------------------: | :----------------------------------------------------------: |
|        `String toLowerCase()`         |                            转大写                            |
|        `String toUpperCase()`         |                            转小写  
`String trim()`                                 |去掉字符串两边空格  
|      `String concat(String str)`      |                   连接两字符串,返回新对象                    |
|    `String[] split(String regex)`     |       将字符串分割为给定的正则表达式匹配,返回一个数组        |
|  `static String valueOf(char[] chs)`  |             返回 `char`参数的字符串 `char`形式。             |
|          `byte[] getBytes()`          | 使用平台的默认字符集将此 `String`编码为字节序列，将结果存储到新的字节数组中。 |
| `byte[] getBytes(String charsetName)` | 使用命名的字符集将此 `String`编码为字节序列，将结果存储到新的字节数组中。 |
|        `char[] toCharArray()`         |                将此字符串转换为新的字符数组。                |
代码:
```java
String s1="aBcDeFgH";
System.out.println(s1.toLowerCase());//转小写
System.out.println(s1.toUpperCase());//转大写
System.out.println(s1.concat(" + 123456"));//拼接两个字符串,返回一个新字符串(新对象)

String s2="2021:5:9.Hello.World";
String [] arr = s2.split(":");//用指定符号来分割字符串为数组
String [] arr1= s2.split("\\.");
System.out.println(Arrays.toString(arr));
System.out.println(Arrays.toString(arr1));
```

运行结果:

```java
abcdefgh
ABCDEFGH
aBcDeFgH + 123456
[2021, 5, 9.Hello.World]
[2021:5:9, Hello, World]
```
代码:
```java
String s2 = "   3空格   ABC   3空格   ";
System.out.println(s2);
System.out.println(s2.trim());//去掉字符串两边空格
```
运行结果:
```java
   3空格   ABC   3空格   
3空格   ABC   3空格
```
代码:

```java
		/*
        public byte[] getBytes() {
            return StringCoding.encode(value, 0, value.length);
        }
        */
        String str = new String("Hello");
        byte [] b = str.getBytes();//编码
        System.out.println(Arrays.toString(b));//[72, 101, 108, 108, 111]

        String strNew = new String(b);//解码
        System.out.println(strNew);//Hello   
```

```java
/*
	public byte[] getBytes(String charsetName)throws UnsupportedEncodingException {
        if (charsetName == null) throw new NullPointerException();
        return StringCoding.encode(charsetName, value, 0, value.length);
    }
*/
String str1 = "张三";
byte [] b1 = str1.getBytes("GBK");//编码
System.out.println(Arrays.toString(b1));//[-43, -59, -56, -3]GBK中1汉字占2字节
String s1 = new String(b1,"GBK" );//解码
System.out.println(s1);//张三
String str1New = new String(b1);//解码(平台默认使用UTF-8编码)  乱码
System.out.println(str1New);//输出����，编码时使用GBK，解码时，默认使用了UTF—8
```

运行结果:

```java
[-43, -59, -56, -3]
张三
����
```

上述代码中输出有乱码,这是什么原因呢?

在编码时,我使用了GBK编码,

**乱码问题**:

编码与解码时使用的编码格式不统一

**解决方法**:

byte [] b = getBytes("编码1");

new String(b,"编码1");

只有编码格式相统一时,才能正常解码

```java
String str1 = "张三";
byte [] b1 = str1.getBytes("GBK");//编码
String str2 = new String(b1,2,2,"GBK");//从第offset个字节开始，到第offset+length个字节结束
System.out.println("截取后："+str2);
```

运行结果:

```java
截取后：三
```


代码:
```java
String str3 = new String("dcba");
System.out.println(str3);//dcba
char[] chars = str3.toCharArray();//将字符串转为字符数组
Arrays.sort(chars);//排序
System.out.println(Arrays.toString(chars));//[a, b, c, d]
System.out.println(chars);//abcd
	/*
       public String(char value[]) {
          this.value = Arrays.copyOf(value, value.length);
        }
      */
System.out.println("=================");

String str3New = new String(chars);//字符数组转为字符串
/*
	public String(char value[]) {
        this.value = Arrays.copyOf(value, value.length);
    }
*/
System.out.println("char[]转字符串："+str3New);
String str4 = String.valueOf(chars);//字符数组转为字符串,返回新对象
/*
	public static String valueOf(char data[]) {
        return new String(data);
    }
*/
System.out.println(str4);
```

运行结果:

```java 
dcba
[a, b, c, d]
abcd
=================
char[]转字符串：abcd
abcd
```
# StringBuffer、StringBuilder
## 1.概述

|          类          |                 值                 |                      |                  |
| :------------------: | :--------------------------------: | :------------------: | :--------------: |
|        String        | 不可变(private final char value[]) |                      |                  |
|     StirngBuffer     |         可变(char[] value)         |         安全         | 多线程操作字符串 |
| StringBuilder(Java5) |         可变(char[] value)         | 不安全(不能同步访问) | 单线程操作字符串 |

- 使用String对字符串修改时,会返回新的字符串对象,在内存中会留下副本,若放在循环中,会大大降低程序效率这时,就可以使用StringBuffer与Stringbuilder

- StringBuilder较于StringBuffer有速度优势,多数情况推荐使用StringBuilder
- 当程序要求线程安全时,使用StringBuffer,适用多线程下在字符缓冲区进行大量操作的情况

## 2.示例

```java
 		StringBuffer s1 = new StringBuffer(5);
        /*
        public StringBuffer(int capacity) {
            super(capacity);
        }
        value = new char[capacity];

         */

        StringBuffer s2 = new StringBuffer();
        /*
        public StringBuffer() {
            super(16);
        }
         */

        //添加
        //append():    return this;  改变的是value数组,不创建新对象
        s1.append("abcde");
        s1.append("12345");
        s1.append("abcde");
        s1.append("12345");
        s1.append("abcde");
        System.out.println("append:"+s1);

        //insert():        return this;
        s2.insert(0,"hello word");
        System.out.println("insert:"+s2);

        //删除    return this;
        s1.delete(5,10);//删除指定区间的字符串:start(包括),end(不包括)
        //s2.deleteCharAt(0);//删除指定位置的字符
        System.out.println("delete:"+s1);

        //替换
        // return this;
        s1.replace(0,5,"hello");
        System.out.println("replace:"+s1);

        //反转字符串
        s2.reverse();
        System.out.println("reverse;"+s2);

		/*
		输出:
		append:abcde12345abcde12345abcde
        insert:hello word
        delete:abcdeabcde12345abcde
        replace:helloabcde12345abcde
        reverse;drow olleh
		*/
```

- StringBuffer与StringBuilder的操作方法完全相同.

- StringBuffer与StringBuilder继承了同一个父类,实现了相同的接口
- StringBuffer添加了synchronized同步锁,多线程安全,StringBuilder没有synchronized同步锁

```java 
/*	StringBuffer

	public final class StringBuffer
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
*/
/*	StringBuilder

	public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
*/
```

