
## 1.String不能改变的原因是什么？

String底层是一个final修饰的char数组，private修饰的属性不能被外界访问，我们无法改变它，也无法影响String的值

![在这里插入图片描述](https://img-blog.csdnimg.cn/44f4bf60186f44cb9832cd381544cf59.png)
初始化的String的地址不能改变，但是地址中的内容可以改变
<font color='red'>这个final修饰的char[] value 不是不可变，数组是引用类型，fianl修饰的引用类型不能指向其他引用，但是本身是可以修改的

因此只要可以访问到String的value数组，就能达到修改String的目的</font>
# 2.String可以改变的方式有哪些？
## 2.1 通过反射修改
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
## 2.2 通过SharedSecrets.getJavaLangAccess()来创建一个不安全的String
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

# 3.String的长度最大是多少？有限制吗？
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
- 由于JVM还需要1个字节表示结束指令，索引的最大值就是`65534`了

**测试字符串定义的最大长度**：65535长度  报错，编译不通过
![在这里插入图片描述](https://img-blog.csdnimg.cn/9dc991e232b248808d8c7286176c879d.png)


**在定义长度为65534的字符串后，对其修改变成65535为什么可以呢？**
![在这里插入图片描述](https://img-blog.csdnimg.cn/3f394ec4ec754895a61fbbedfd3f00d4.png)
①定义的String长度最大为65534 正常运行√
②长度+1 = 65535，超过了最大范围，但这是在运行时创建的65535长度的字符串，是允许的，只是在编译时不允许定义超过65534的字符串






