# String如何实现的不可变？

## 1.fianl修饰的属性

String底层是一个final修饰的char数组，private修饰的属性不能被外界访问，我们无法改变它，也无法影响String的值

![在这里插入图片描述](https://img-blog.csdnimg.cn/44f4bf60186f44cb9832cd381544cf59.png)
初始化的String的地址不能改变，但是地址中的内容可以改变
<font color='red'>这个final修饰的char[] value 不是不可变，数组是引用类型，fianl修饰的引用类型不能指向其他引用，但是本身是可以修改的

因此只要可以访问到String的value数组，就能达到修改String的目的</font>

## 2.通过拷贝实现不可变

**substring方法中调用了构造方法来返回一个新的String对象，该构造方法中调用了Arrays数据工具类的拷贝方法，将传入的char数组，赋给了final修饰的vlaue数组**



1. `substring(int beginIndex)`将字符串从指定索引处截取，返回截取的部分

```java
public String substring(int beginIndex) {
    ......
    //返回的String是new了一个新的，不是返回的原来对象
    return (beginIndex == 0) ? this : new String(value, beginIndex, subLen);
}
```

2. `String(char value[], int offset, int count) `
   将传入的字符数组部分变成字符串，offset是起始位置，count是范围大小

```java
 public String(char value[], int offset, int count) {
     ......
     //该构造方法中，要对final修饰的value操作时，将传入的value数组拷贝一份赋给了源码中的value数组
     this.value = Arrays.copyOfRange(value, offset, offset+count);
 }
```



# String可以改变的方式有哪些？
## 1 通过反射修改
### 代码演示

通过反射可以获取到String的私有属性value

```java
public class Test   {
    public static void main(String[] args) {
        String a = "hello";
        String b = "world";
        System.out.println(a+"的hashcode= "+a.hashCode());
        try {
            //反射获取string的私有属性value数组
            Field value = a.getClass().getDeclaredField("value");
            //设置value数组可以访问
            value.setAccessible(true);
            //获取value属性里面的值(内存地址)
            char[] temp = (char[])value.get(a);
            //通过temp的地址引用 找到真实String对象中的数组，修改数组内的元素
            value.set(a,new char[]{'x','x','x','x','x'});
            System.out.println(a+"的hashcode= "+a.hashCode());
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("a="+a+",b="+b);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/8024723e99074b638cdb440270cc1ea1.png)
观察输出，字符串a已经通过反射拿到value数组修改过了

还有一个问题，修改前后的 a 的hash相同。

### String的值修改了，它的hashCode为什么不变？
hash值不等于地址，String的hash码是通过对每个字符进行一定规则累加得到的
**可以说一个String对象的hashCode()只会执行一次，因此它的值改变后，hash码还是原来的**，为什么这么说？
1. String中有一个hash属性，当第一次调用hashCode()时，hash为0，计算hash码，将hash码赋给hash，并返回hash码
2. 当后面再次调用hashCode()时，返回的hash值其实时第一次计算出来的结果

来看hashCode()的源码
```java
public int hashCode() {
	//拿到String的hash
    int h = hash;
    //hash=0就去计算这个string的hashCode码
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        //将hashCode结果赋给hash
        hash = h;
    }
    //hash不等于0，也就是这个String对象之前已经执行过hashCode()了，直接返回hash属性
    return h;
}
```
总结：<font color='red'>不能用hashCode()来判断是否是同一个字符串<font>
## 2 通过SharedSecrets.getJavaLangAccess()来创建一个不安全的String
### 代码演示
```java
public class TestString {
    public static void main(String[] args) {
        char[] chars = new char[]{'h','e','l','l','o'};
        String str = SharedSecrets.getJavaLangAccess().newStringUnsafe(chars);

        StringHolder holder = new StringHolder(str);
        System.out.println(holder.value);//hello

        for (int i = 0; i < chars.length; i++) {
            chars[i] = '?';
        }
        System.out.println(holder.value);//?????

        for (int i = 0; i < chars.length; i++) {
            chars[i] = '#';
        }
        System.out.println(holder.value);//#####
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/338b3e704402439cadc91c533c293c9b.png)
工作流程
![在这里插入图片描述](https://img-blog.csdnimg.cn/1aaf182302424850acb9057c6ea5d7b7.png)

# String的长度最大是多少？有限制吗？
<font color='red'>
<list>
<li>在运行期间：最大长度是2^31-1
<li>在编译期间：最大长度是2^16-1-1 (25534)，又减1是因为JVM需要1个字节表示结束指令

<li><font color='blue'>定义String时，最大长度只能是65534

<li><font color='blue'>只有在程序运行时，拼接到长度为2^31-1是允许的<font>
<font>



String底层是一个字符数组char[] value
数组索引是整数类型，int 最大值是2^31 -1
数组长度范围：【0 ~  (2^31-1)】

JVM中对class文件格式及常量池中对String的结构定义：
- 对索引定义了u2，即无符号占2字节；2字节最大值是2^16-1，也就是`65535`	
- 由于JVM还需要1个位置表示结束指令，索引的最大值就是`65534`了

**测试字符串定义的最大长度**：65535长度  报错，编译不通过
![在这里插入图片描述](https://img-blog.csdnimg.cn/9dc991e232b248808d8c7286176c879d.png)


**在定义长度为65534的字符串后，对其修改变成65535为什么可以呢？**
![在这里插入图片描述](https://img-blog.csdnimg.cn/3f394ec4ec754895a61fbbedfd3f00d4.png)
①定义的String长度最大为65534 正常运行√
②长度+1 = 65535，超过了最大范围，但这是在运行时创建的65535长度的字符串，是允许的，只是在编译时不允许定义超过65534的字符串





# String占空间大小

String占的空间包含String对象本身和内部的char数组(16字节)、int类型的hash(4字节)、long类型的序列化UID(8字节)

<img src="../img/image-20221101122058429.png" alt="image-20221101122058429" style="zoom:50%;" />



数组 = 8 + 4 + 4 = 16字节

所以String所占空间 = **mark word** 8字节+ **class pointer** 4字节 +  实例数据(char[]、int、long ：16+4+8) 28字节 = 40



# intern()方法

```java
String s1 = new StringBuilder("hello ").append("word").toString;
System.out.println(s1 == s1.intern());
```

intern() 方法是一个 native 的方法，返回常量池中的字符串引用，主要体现在以下两点：

1. 如果常量池中已存在该字符串，则直接返回常量池中该对象的引用。
2. 如果常量池中不存在该字符串，则在常量池中加入该对象引用并返回。

```java
public class StrIntrenTest {
    
    public static void main(String[] args) {
        // 1. 字面量创建形式
        String s1 = "jmcui";
        //  1. 在字符串常量池中生成字符串"jmcui"实例
        //  2. 将栈中的 s1 指向字符串常量池中的字符串"jmcui"实例

        //因为字面量的创建方式是在字符串常量池中生成实例，而 intern() 方法返回常量池中的字符串引用，两个引用自然是同一个
        System.out.println("s1 == s1.intern() ：" + (s1 == s1.intern())); // true

        // 2. new 创建方式
        String s2 = new String("jmcui");
        //  1. 在Java堆中生成字符串"jmcui"实例
        //  2. 将栈中的 s2 指向Java堆中的字符串"jmcui"实例

        //因为 new String() 的方式是在堆（Heap）上创建实例，二者不是同一个引用
        System.out.println("s1 == s2 ：" + (s1 == s2)); // false
        
        //equals 方法是用来比较的是两个字符串的内容是否相等
        System.out.println("s1.equals(s2) ：" + s1.equals(s2)); // true
        
        //当 s2 调用 intern() 方法的时候，发现常量池中已经存在该字符串，则直接返回了该引用（s1 的引用）
        System.out.println("s1 == s2.intern()：" + (s1 == s2.intern())); // true

        // 3. StringBuilder/StringBuffer 方式和 new 方法类似
        String s3 = new StringBuilder("jm").append("cui").toString();
        //  1. 在Java堆中生成字符串【"jmcui"】实例  2. 将栈中的 s3 指向Java堆中的字符串"jmcui"实例
        
        //StringBuilder/StringBuffer 创建方式是在堆（Heap）上创建字符串实例，二者不是同一个引用。
        System.out.println("s1 == s3 ：" + (s1 == s3)); // false
        
        //s2 和 s3 都是 new 出来的字符串实例，在堆（Heap）上存储不同的位置，自然不是同一个实例。
        System.out.println("s2 == s3 ：" + (s2 == s3)); // false
        
        //s2 的 intern() 返回的是 s1 的引用，s3 的 intern() 也是一样的道理，因此 s2 的 intern() 和 s3 的 intern() 返回的都是 s1 的引用，所以相等
        System.out.println("s2.intern() == s3.intern() ：" + (s2.intern() == s3.intern())); // true
    }
}
```



- jdk1.6：intern()方法会把首次遇到该字符串的实例复制到永久代，返回永久代中该字符串实例的引用
- jdk1.7：intern()不会复制实例，只是在常量池中记录首次出现的实例引用

```java

String s = new StringBuilder("软件开发").toString();
System.out.println(s == s.intern());//false

String s1 = new StringBuilder("软件").append("开发").toString();
//1.6为false   1.7为true
System.out.println(s1 == s1.intern());//true

String s2 = new StringBuilder("ja").append("va").toString();
//1.6 1.7都为false
//sun.misc.Version类中有个"java"常量，已经在常量池了
System.out.println(s2 == s2.intern());//false
```

- 为什么s == s.intern() 输出 false，s1 == s1.intern() 输出为true？

  因为new StringBuilder("软件").append("开发").toString();在toString()之前字符串常量池并没有"软件开发",在toString()后生成"软件开发"这个字符串，整个过程只有一个对象，返回的是同一个引用

  而new StringBuillder("软件开发").toString();在toString()之前，已经在字符串常量池中存在了"软件开发"字符串了，s.intern()返回的是首次出现的引用地址,s是toString()后的新字符串的引用地址，所以为false



# StringBuilder和StringBuffer区别

String是不可变的char数组，对String进行操作时不会改变原对象，只会创建新的String对象，StringBuilder和StringBuffer是在原对象上进行操作

String在拼接时："hello"+"world"，底层会创建StringBuilder对象，调用StringBuilder的append()方法

StringBuilder和StringBuffer都是继承自AbstractStringBuilder

1. **线程安全**：StringBuilder线程不安全，StringBuffer的public方法都是synchronized关键字修饰，是线程安全的
2. **缓冲区**：StringBuilder每次toString()都需要复制一次字符数组，再构造一个字符串；StringBuffer每次toString()都会使用缓存区的toStringCache来构造字符串，StringBuffer的toString()也是同步的
3. **性能**：StringBuilder性能更好，但是多线程下推荐使用StringBuffer



# String的常用方法

| String的判断功能                      | 作用                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| `equals()`                            | 判断两个对象内容是否相等                                     |
| `contains()`                          | 判断是否包含指定字符串                                       |
| `isEmpty()`                           | 判断字符串长度是否为0                                        |
| `startswith()`                        | 判断是否以指定的字符串开头                                   |
| `endswith()`                          | 判断是否以指定的字符串结尾                                   |
| `int length()`                        | 返回字符串长度,字符串底层用char型数组存储                    |
| `char charAt(int index)`              | 返回指定索引的字符                                           |
| `int indexOf(int ch)`                 | 返回指定字符首次出现的位置                                   |
| `int indexOf(int ch, int fromIndex)`  | 从指定索引开始,返回指定字符首次出现的位置                    |
| `int lastIndexOf(int ch)`             | 返回指定字符在字符串中最后一次出现处的位置，如果此字符串中没有这样的字符，则返回 -1 |
| `String substring(int start)`         | 截取指定区间内字符串,   从开始索引~s.length-1                |
| `String substring(int start,int end)` | 截取指定区间内字符串, 从开始索引~结束索引-1                  |
| `String trim()`                       | 去掉字符串两边空格                                           |
| `String concat(String str)`           | 连接两字符串,返回新对象                                      |
| `String[] split(String regex)`        | 将字符串分割为给定的正则表达式匹配,返回一个数组              |
| `byte[] getBytes()`                   | 使用平台的默认字符集将此 `String`编码为字节序列，将结果存储到新的字节数组中。 |
| `char[] toCharArray()`                | 将此字符串转换为新的字符数组。                               |









