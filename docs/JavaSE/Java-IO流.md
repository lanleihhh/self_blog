# IO流
## 1. 概述

输入输出(I/O):

Input:输入,将硬盘中的数据**读入**程序中,进行程序的read操作

Output:输出,从程序往外部设备**写**数据,进行程序的write操作

分类:

从数据流编码格式上可以分为**字节流**与**字符流**

- 字节流:InputStream和OutputStream的子类,读写二进制文件,处理音频,图片;处理单元为1个字节 字节流将读取到的字节数据，去指定的编码表中获取对应文字
- 字符流:Reader和Writer的子类,处理字符或字符串,处理单元为一个字符.

按照数据流的传输方向分为**输入流**(从程序中往进读)和**输出流**(从程序中往外写)

按照流的功能不同分为**节点流**与**处理流**

节点流

 

处理流



IO体系图如下:

![在这里插入图片描述](https://img-blog.csdnimg.cn/6b843df74e474232931f7fe482a5b307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 2. 节点流

### 输入输出字节流

#### InputStream的常用方法

| InputStream的常用方法                      | 说明                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| int read() throws IOException              | 读取一个字节并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 |
| int read(byte[] buffer) throws IOException | 读取一系列字节并存储到一个数组buffer， 返回实际读取的字节数，如果读取前已到输入流的末尾返回-1 |
| void close() throws IOException            | 关闭流释放内存资源                                           |

```java 
//首先告诉java输入流去读计算机上的哪个文件
File file =new File("E:/temp/1.txt");
//使用字节输入流读取文件
FileInputStream in = new FileInputStream(file);
for (int i = 0; i < 5; i++) {
    System.out.print((char)in.read());//从文件中每read一次,输入一个字节
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/5b8a2400dc0649a0929b03c2b947ad33.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/d820988842e14fc3b43d51c2cbb4f6fa.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


#### OutputStream的常用方法

| OutputStream的常用方法                                    | 说明                                                         |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| void write(int b) throws IOException                      | 向输出流中写入一个字节数据,该字节数据为参数b的低8位          |
| void write(byte[] b, int off, int len) throws IOException | 将一个字节类型的数组中的从指定位置（off）开始的 len个字节写入到输出流 |
| void close() throws IOException                           | 关闭流释放内存资源                                           |

```java
File file1 = new File("E:/temp/2.txt");
//使用字节输出流写入
FileOutputStream out = new FileOutputStream(file1);
//循环读写   从上次读取的位置开始
for (int i=5;i<file.length();i++){
	out.write(in.read());
}
```

执行后:

![在这里插入图片描述](https://img-blog.csdnimg.cn/9e667cde381b47a5af7ec2f7894702ca.jpg#pic_center)


```java
System.out.println("复制的新文件字节: "+file1.length());
FileInputStream in1 = new FileInputStream(file1);
System.out.println("复制的新文件内容:");
for (int i = 0; i < file1.length(); i++) {
	System.out.print((char)in1.read());
}
in.close();//读取工作完成,关闭输入流
in1.close();
out.close();//关闭输出流
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/932ebfa1cfd345b9b4ef0e2964e9b487.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


#### read()/write(int b)

```java
package iostreamdemo;
import java.io.*;
public class IoStreamDemo2 {
    public static void main(String[] args){
        
        FileInputStream in=null;
        FileOutputStream out=null;
        File f = new File("E:/temp/3.txt");
        try {
            in = new FileInputStream("E:/temp/1.txt");
            out= new FileOutputStream(f);
            int a = 0;//记录read()
            //一直读取直到读完内容   read()读到文件末尾返回-1
            while ((a=in.read())!=-1){
               out.write(a);
            }
            System.out.println("写入的新文件字节: "+f.length());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
            System.out.println("文件未找到");
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("读写异常");
        } finally {
            try {
                if(in!=null){
                    in.close();
                }
                if(out!=null){
                    out.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        
    }
}
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/8b656684a8aa4c7bb69e4e54371f36be.jpg#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/04ef392597fd469ab6b48c3109056a71.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


#### read(byte[] b)/write(byte[] b, int off, int len)

```java
package iostreamdemo;
import java.io.*;
public class IoStreamDemo3 {
    public static void main(String[] args) {

        /*
        read()
        每次只读一个字节并返回,读完返回-1 ,效率低

        read(byte[] b)
        每次最多读byte的数组长度个字节,返回数组中实际装入的字节个数
        266,读完返回-1
         */

        FileInputStream in=null;
        FileOutputStream out=null;
        File f = new File("E:/temp/4.txt");

        try {
             in= new FileInputStream("E:/temp/1.txt");
             out= new FileOutputStream(f);

            byte [] b = new byte[15];
            //记录数组元素数
            int length=0;
            //read(byte[],开始位置,数组长度)
            while ((length=in.read(b))!=-1){
                out.write(b,0,length);
                //每次向外写一个byte个字节,从指定位置开始(一般情况从0开始,向外写length个长度)
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
            System.out.println("文件未找到");
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("读取异常");
        } finally {
            try {
                if(in!=null){
                    in.close();
                }
                if(out!=null){
                    out.close();
                }

            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/75396f3d3ff84af181f785897907442e.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


### 输入输出字符流



#### Reader的基本方法

| 方法                | 声明                                                         |
| ------------------- | ------------------------------------------------------------ |
| `read()`            | 读一个字符,以整数返回,如果读取前已到输入流的末尾返回-1       |
| `read(char[] cbuf)` | 将字符读入数组,返回实际读取的字符,如果读取前已到输入流的末尾返回-1 |
| `close()`           | 关闭流并释放与之相关联的任何系统资源                         |

#### Writer的基本方法

| 方法                                    | 声明                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| `write(int c)`                          | 写入一个字符                                                 |
| `write(String str)`                     | 写入一个字符串                                               |
| `write(String str,  int off, int len)`  | 将一个字符串类型的数组中的从指定位置（offset）开始的 length个字符写入到输出流 |
| `write(char[] cbuf)`                    | 写入一个字符数组。                                           |
| `write(char[] cbuf,  int off, int len)` | 将一个字符类型的数组中的从指定位置（offset）开始的 length个字符写入到输出流 |
| `flush()`                               | 刷新流                                                       |
| `close()`                               | 关闭流(先刷新)                                               |

```java
//新建空文件
File file = new File("E:/temp/6.txt");
//新建字符输入流
FileReader fileReader = new FileReader("E:/temp/1.txt");
//新建字符输出流
FileWriter fileWriter = new FileWriter(file);
```

#### read()/write(int c)

```java 
int a = 0;
System.out.println("文件内容: ");
//当读到文件末尾时,结束读取
while ((a=fileReader.read())!=-1){
    //将读取到的字符传给输出流对象,写入目标文件
    fileWriter.write(a);
    System.out.print((char)a);
}
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/6a81b368796e4afd96fb82de7c4ca87e.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/504451f578084460853f5fd9eab78461.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




#### read(char[] cbuf)/write(char[] cbuf,  int off, int len)

```java 
		File file = new File("E:/temp/7.txt");
        FileReader fileReader = new FileReader("E:/temp/1.txt");
        FileWriter fileWriter = new FileWriter(file);
		
            char [] chars = new char[5];
            int length = 0;
            while ((length=fileReader.read(chars))!=-1){
                fileWriter.write(chars,0,length);
            }

            fileReader.close();
            fileWriter.close();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ee3ddbab14dc4f348327e900e2bbfd63.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 3. 处理流

 对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写 

常用类

缓冲流Buffer--

- BufferedInputStream

- BufferedOutputStream



```java
package iostreamdemo;
import java.io.*;
public class BufferedDemo1 {
    public static void main(String[] args) {
        
        BufferedInputStream bin=null;
        BufferedOutputStream bout=null;
        
        try {
            //原始底端通道
            FileInputStream in = new FileInputStream("E:/temp/1.txt");
            //用带缓冲区的高级管道包装
             bin= new BufferedInputStream(in,2048);

            FileOutputStream out = new FileOutputStream("E:/temp/8.txt");
            bout=new BufferedOutputStream(out,2048);

            byte [] bytes = new byte[1024];
            int length=0;
            while ((length=bin.read(bytes))!=-1){
                bout.write(bytes, 0, length);
            }
            
            bin.close();
            bout.flush();//刷新缓冲区
            bout.close();//先刷新再关闭

        } catch (FileNotFoundException e) {
            e.printStackTrace();
            System.out.println("文件未找到");
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("读取异常");
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/626f73a298bb4388b8f6a364cb02f1a5.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


- BufferedReader

- BufferedWriter

新建流

```java
 File file1 = new File("E:/temp/9.txt");
FileReader fileReader = new FileReader(file1);
BufferedReader bufferedReader = new BufferedReader(fileReader);

File file2 = new File("E:/temp/10.txt");
//FileWriter fileWriter = new FileWriter(file2);//this(file, false); 每运行一次,写入数据会覆盖上一次的数据
FileWriter fileWriter = new FileWriter(file2,true);//(运行多次)追加,不会覆盖
BufferedWriter bufferedWriter = new BufferedWriter(fileWriter);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/3434b2d3be63401f88f38dcea384d74e.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


读写

```java
String line = null;
int i =0;
while ((line=bufferedReader.readLine())!=null){//当读一行为null时,结束循环
    //输出读取的文件内容
    System.out.println("第"+(++i)+"行:"+line);
    //一次写入一行数据
    bufferedWriter.write(line);
    //换行
    bufferedWriter.newLine();
}
```

关闭流

```java
bufferedReader.close();//关闭输入缓冲流
bufferedWriter.close();//关闭输出缓冲流
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/bfc17c8f9e8f4af1978e62054b17c510.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/d29c5377770a49e28e757fe396d8dc1a.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


我们再执行一次程序 ,目标文件变为:

![在这里插入图片描述](https://img-blog.csdnimg.cn/3798ed76c4534e089a9889e8ebd81aa4.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




==注意==:FileOutputStream及子类构造方法(File file, boolean append) 

append=**false**  或**不写**该参数   表示写入会覆盖上一次的内容

append=**true**                             表示写入会在原文件末尾添加




## 4. Print流

Print 打印流：只做输出没有输入 打印流分为字节打印流和字符打印流 

PrintWriter：字符打印流 print方法可以打印各种类型数据  

```java
       /*
        打印流:只有输出,没有输入
        PrintWriter:字符打印流
        Print:打印各种数据
         */

		//PrintStream
        PrintStream printStream = new PrintStream("E:/temp/html/1.html");
        printStream.println("<h1>一级标题</h1>");
        printStream.println("<h2>二级标题</h2>");
        printStream.println("<h3>三级标题</h3>");
        printStream.println("<h4>四级标题</h4>");
        printStream.println("<h5>五级标题</h5>");
        printStream.close();
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/14f8c621d6784bb491e6770b1ca77694.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


```java
 		/*
        PrintWriter
         */
        PrintWriter printWriter = new PrintWriter("E:/temp/html/2.txt");
        printWriter.println("呼吸之野");
        printWriter.println("寻宝游戏");
        printWriter.println("青年晚报");
        printWriter.println("不如吃茶去");
        printWriter.println("梦游记");
        printWriter.println("苏格拉没有底");
        printWriter.println("寻雾启事");
        printWriter.println("自定义");
        printWriter.close();
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/73d29dc76c5b44aa93680e19caef9002.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 5. 对象输入输出流

 对象的输入输出流 : 主要的作用是用于写入对象信息与读取对象信息。 

对象信息 一旦写到文件上那么对象的信息就可以做到持久化了 

对象的输出流： ObjectOutputStream 

对象的输入流: ObjectInputStream 

 要将序列化之后的对象保存下来，需要通过对象输出流(ObjectOutputStream) 将对象状态保存，之后再通过对象输入流(ObjectInputStream)将对象状态恢复。 在ObjectInputStream 中用readObject()方法可以直接读取一个对象， ObjectOutputStream中用writeObject()方法可以直接将对象保存到输出流中 

**创建一个学生类**

```java
package objout;
import java.io.Serializable;
/*
    若需将一个类的对象信息序列化到文件中时,此类需要生成一个序列化ID号(版本号)
    implements Serializable 接口,默认会在类中生成一个版本号
    一旦类中的信息发生改变,版本号自动变化
    (实现接口,可以显式生成版本号,当类信息变化时,版本号不变)
 */
public class Student implements Serializable {

    private static final long serialVersionUID = -5854781956022497290L;
    private String xuehao;
    private String name;
    //关键字 transient 在对象序列化时,忽略此属性
    //private transient String name;
    

    //int temp;//测试有无版本号时,增删信息对InvalidClassException异常的影响
    public Student(String xuehao, String name) {
        this.xuehao = xuehao;
        this.name  = name;
    }

    public String getXuehao() {
        return xuehao;
    }

    public void setXuehao(String xuehao) {
        this.xuehao = xuehao;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{" +
                "xuehao='" + xuehao + '\'' +
                ", name='" + name + '\'' +
                '}';
    }
}
```

**对象输出流**

```java
//实例化两个学生对象
Student student1 = new Student("1001", "张三");
Student student2 = new Student("1002", "李四");

//文件输出流
FileOutputStream fileOutputStream = new FileOutputStream("E:/temp/studentInfo.txt");
//对象输出流
ObjectOutputStream objectOutputStream = new ObjectOutputStream(fileOutputStream);
//将对象输出到文件中---序列化
objectOutputStream.writeObject(student1);
objectOutputStream.writeObject(student2);
//关闭流
objectOutputStream.close();
```

**对象输入流**

```java
//创建文件输入流
FileInputStream fileInputStream = new FileInputStream("E:/temp/studentInfo.txt");
//创建对象对象输入流
ObjectInputStream objectInputStream = new ObjectInputStream(fileInputStream);
//读取文件中的对象,读入程序中---反序列化
Student student1 = (Student)objectInputStream.readObject();
Student student2 = (Student)objectInputStream.readObject();
//关闭流
objectInputStream.close();
//输出对象
System.out.println(student1);
System.out.println(student2);
/*
报错:
InvalidClassException
原因: 类中信息改变
解决: 显式地生成版本号,类信息改变不再报错
*/
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/327079f99da548889e71e6fd5bc16aab.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




```java
package objout;
import java.io.*;
import java.util.Date;
public class ObjectOutputStreamDemo1 {
    private static Object FileInputStream;

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        /*
        将程序运行时那一刻的时间保存到文件中
        直接将对象信息存起来-->对象序列化
         */

        //对象输出流
        //实例化日期类
        Date date = new Date(1299999123123L);
        //文件输出流
        FileOutputStream fileOutputStream = new FileOutputStream("E:/temp/date.txt");
        //对象输出流
        ObjectOutputStream objout = new ObjectOutputStream(fileOutputStream);//对象序列化
        //将对象写入文件--序列化
        objout.writeObject(date);//写入参数为Object的对象
        //关闭对象输出流
        objout.close();


        /*
        对象输入流
         */
        FileInputStream fileInputStream = new FileInputStream("E:/temp/date.txt");
        ObjectInputStream objin = new ObjectInputStream(fileInputStream);

        Object obj = objin.readObject();//将对象反序列化

        if(obj instanceof Date){//判断obj是否是Date类型
            Date date1 = (Date)obj;//向下强转
            System.out.println(date1);
        }

    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/12be9d525f0f4aeb97ceac25fecd670b.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)








## 6.  对象序列化 

  对象的寿命通常随着生成该对象的程序的终止而终止。 

有时候，可能需要将对象的状态保存下来，在需要时再将对象恢复。 

对象的输出流将指定的对象写入到文件的过程，就是将对象序列化的过程，

对象 的输入流将指定序列化好的文件读出来的过程，就是对象反序列化的过程。

既然 对象的输出流将对象写入到文件中称之为对象的序列化，所以必须要实现 Serializable接口。 

Serializable接口中没有任何方法。

当一个类声明实现Serializable接口后， 表明该类可被序列化。 

在类中可以生成一个编号 private static final long serialVersionUID = -5974713180104013488L; 

随机生成 唯一的 serialVersionUID 用来表明实现序列化类的不同版本间的兼容性。

某个类在 与之对应的对象已经序列化出去后做了修改，该对象依然可以被正确反序列化 
