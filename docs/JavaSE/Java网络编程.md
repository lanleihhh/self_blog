
注意:网络编程 ==≠== 网站编程,网络编程是使用==套接字==socket来达到==进程间通信==,一般称==TC/IP编程==

# 一. 计算机网络

## 1.概念

 计算机网络： 把分布在==不同地理区域==的具有==独立功能的计算机==,通过==通信设备与线路==连接起来，由功能完善的软件实现==资源共享==和==信息传递==的系统 







# 二.  IP和端口号  

## IP

 IP 地址：InetAddress 

- IP地址是由IP使用的32位或128位无符号数字，构建UDP和TCP协议的低级协议。
- 全球地址在互联网上是独一无二的

- 唯一的标识 Internet 上的计算机 

- 本地回环地址(hostAddress)：127.0.0.1 

- 主机名(hostName)：localhost 

IP地址：公网地址（万维网使用）和私有地址（局域网使用），192.168. 开头的就是私有址址，范围即为**192.168.0.0-192.168.255.255**，专门为组织机构内 部使用 

特点：**不易记忆**  

## 端口号

 **端口号**标识正在计算机上运行的进程（程序） 

 不同的进程有不同的端口号 

 被规定为一个 16 位的整数 0~6553 

 端口**分类**： 

**公认端口**：0-1023，被预先定义的服务通信占用（如：HTTP占用端口80， FTP占用端口21，Telnet占用端口23） **注册端口**：1024-49151，分配给用户进程或应用程序。（如：Tomcat占用端 口8080，MySQL占用端口3306，Oracle占用端口1521等）。 

**动态/私有端口**：49152-65535. 

# 三.  InetAddress类 



 Internet上的主机有两种方式表示地址： 

域名(hostName)：www.baidu.com

 IP 地址(hostAddress)：220.181.111.37 

InetAddress类主要表示IP地址，两个子类：Inet4Address、Inet6Address。

 InetAddress 类 对 象 含 有 一 个 Internet 主 机 地 址 的 域 名 和 I P 地 址 ： www.baidu.com和 220.181.111.37 

域名容易记忆，当在连接网络时输入一个主机的域名后，域名服务器（DNS）负责 将域名转化成IP地址，这样才能和主机建立连接。 -------**域名解析** 

方法:

| 方法                                        | 说明                                   |
| ------------------------------------------- | -------------------------------------- |
| ` String getHostAddress()`                  | 返回 IP 地址字符串（以文本表现形式）   |
| `  String getHostName()`                    | 返回该IP地址的主机名                   |
| `static InetAddress getByName(String host)` | 在给定主机名的情况下返回主机的 IP 地址 |
| `static InetAddress getLocalHost()`         |           返回本地主机名                             |

```java
try {
    //新建InetAddress对象
    InetAddress ia = InetAddress.getByName("www.baidu.com");
    //返回IP地址的主机名
    System.out.println("百度主机名:"+ia.getHostName());
    //返回IP地址字符串
    System.out.println("百度IP地址:"+ia.getHostAddress());
} catch (UnknownHostException e) {
	e.printStackTrace();
}
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/73e97e24433b45bba69818a0a8e2c971.jpg#pic_center)


```java
try {
            //本地回环地址
            InetAddress ia1 = InetAddress.getByName("127.0.0.1");
            //返回IP地址的主机名
            System.out.println(ia1.getHostName());
            //返回IP地址字符串
            System.out.println(ia1.getHostAddress());
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/89a5484ac37e435db189544ce89afead.jpg#pic_center)


```java
try {
            InetAddress ia1 = InetAddress.getLocalHost();
            //返回IP地址的主机名
            System.out.println("本地主机名: "+ia1.getHostName());
            //返回IP地址字符串
            System.out.println("本地IP地址: "+ia1.getHostAddress());
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/fc8237e877914ef68e36912a6c874f73.jpg#pic_center)








# 四.  网络通信协议 

### 1.  协议

 计算机网络中实现通信必须有一些约定，即通信协议，对速率、传输代 码、代码结构、 传输控制步骤、出错控制等制定标准. 如**TCP/IP协议**

### 2. 协议分层

分层思想:

 由于结点之间联系很复杂，在制定协议时，把复杂成份分解成一些简单 的成份，再将它们复合起来。最常用的复合方式是层次方式，即同层间可 以通信、上一层可以调用下一层，而与再下一层不发生关系。各层互不影 响，利于系统的开发和扩展。  

OSI参考模型

![在这里插入图片描述](https://img-blog.csdnimg.cn/a82219d228114463a60ba45d348fb7d8.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)






 我们编写的程序位于应用层，因此我们的程序是与TCP/UDP打交道的 

TCP/IP协议

TCP:传输控制协议（Transmission Control Protocol） 

IP:IP(Internet Protocol)协议是网络层的主要协议，支持网间互连的数据通信。 

### 4. TCP/UDP

- TCP是可靠的连接，TCP就像打电话，需要先打通对方电话，等待对方有回应后才会跟对方继续说话，也就是一定要确认可以发信息以后才会把信息发出去。TCP上传任何东西都是可靠的，只要两台机器上建立起了连接，在本机上发送的数据就一定能传到对方的机器上。

- UDP就好比发电报，只负责发送出去，不管对方有没有收到，所以UDP是不可靠的。

- TCP传送数据虽然可靠，但传送得比较慢；
- UDP传送数据不可靠，但是传送得快。

#### TCP

 使用TCP协议前，须先建立TCP连接，形成传输数据通道 

- 传输前，采用“三次握手”方式，是可靠的 
- TCP协议进行通信的两个应用进程：客户端、服务端 
-  在连接中可进行大数据量的传输
- 传输完毕，需释放已建立的连接,**效率低** 
- 在断开时要进行“四次挥手”  

**三次握手**



![在这里插入图片描述](https://img-blog.csdnimg.cn/f746f7feec7c4ad59c1b6deb2e81bf04.gif#pic_center)


**四次挥手**

![在这里插入图片描述](https://img-blog.csdnimg.cn/83b1d03eb2254726af3d524959abcec1.webp?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)






#### UDP

- 将数据、源、目的封装成数据包，不需要建立连接 

- 每个数据报的大小限制在64K内 
- 因无需连接，故是**不可靠**的 
- 发送数据结束时无需释放资源，速度快  





# 五. Java网络编程

 Java提供的网络类库，可以实现网络连接，联网的底层细节被隐藏在 Java 的本机安装系统里，由 JVM 进行控制。并且 Java 实现了一个 跨平台的网络库，程序员面对的是一个统一的网络编程环境 

 网络编程的目的： 直接或间接地**通过网络协议**与其它计算机进行**通讯** 

 网络编程中有两个主要的问题： 

- 如何准确地定位网络上一台或多台主机 
- 找到主机后如何可靠高效地进行数据传输

# 六. Socket套接字

## 概述

 利用套接字(Socket)开发网络应用程序早已被广泛的采用，以至于成为 事实上的标准。 

- 通信的两端都要有Socket，是两台机器间通信的端点 

-  网络通信其实就是Socket间的通信。 

-  Socket允许程序把网络连接当成一个流，数据在两个Socket间通过IO传 输。

- 一般主动发起通信的应用程序属**客户端**，等待通信请求的为**服务端**. 

 Java语言的基于**套接字编程**分为服务端编程和客户端编程，其通信模型 如图所示 

![在这里插入图片描述](https://img-blog.csdnimg.cn/f087b2f92f3143238603963848486edf.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)

## TCP套接字
### 客户端

客户端程序使用Socket类创建对象,创建的同时会自动向服务器方发起连接

Socket构造方法:

-  `Socket(InetAddress address, int port)`     根据指定服务端的 IP 地址或端口号构造 Socket 类对象 
- `Socket(String host,int port)`  向服务器(域名为host,端口号为port)发起TCP连接,若成功,创建socket对象,否则抛出异常

| Socket常用方法                   | 说明                 |
| -------------------------------- | -------------------- |
| `  InputStream getInputStream()` | 返回此套接字的输入流 |
| `OutputStream getOutputStream()` | 返回此套接字的输出流 |
| `void close()`                   | 关闭此套接字         |

客户端Socket的工作过程包含以下四个基本的步骤： 

1. 创建 Socket：根据指定服务端的 IP 地址或端口号构造 Socket 类对象。若服务 器端响应，则建立客户端到服务器的通信线路。若连接失败，会出现异常。 

2. 打开连接到 Socket 的输入/出流： 使用 getInputStream()方法获得输入流， 使用 getOutputStream()方法获得输出流，进行数据传输 

3. 按照一定的协议对 Socket 进行读/写操作：通过输入流读取服务器放入线路的 信息（但不能读取自己放入线路的信息），通过输出流将信息写入线程。

4. 关闭 Socket：断开客户端到服务器的连接，释放线路  



### 服务器端

 服务器程序建立 **ServerSocket** 对象 

**ServerSocket对象**负责等待客户端请求建立套接字连接，类似邮局某个窗口中的业务员。 也就是说，**服务器必须事先建立一个等待客户请求建立套接字连接的ServerSocket对象**。 

所谓“接收”客户的套接字请求，就是accept()方法会返回一个 Socket 对象 

ServerSocket的方法 	

构造方法:

​    `ServerSocket(int port)`  

  创建绑定到指定端口的服务器套接字

| ServerSocket常用方法 | 说明                                               |
| -------------------- | -------------------------------------------------- |
| ` Socket accept()`   | 监听套接字连接,成功返回套接字,否则阻塞直到连接成功 |
| `void close()`       | 关闭套接字                                         |

 服务器程序的工作过程包含以下四个基本的步骤： 

1. 调用 ServerSocket(int port) ：创建一个服务器端套接字，并绑定到指定端 口上。用于监听客户端的请求。 

2. 调用 accept()：监听连接请求，如果客户端请求连接，则接受连接，返回通信 套接字对象。 

3. 调用 该Socket类对象的 getOutputStream() 和 getInputStream ()：获取 输出流和输入流，开始网络数据的发送和接收。 

4. 关闭ServerSocket和Socket对象：客户端访问结束，关闭通信套接字。 

现在分别建立客户端和服务器端程序

服务器端程序代码

```java
package networkdemo.tcp;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Scanner;

public class ServerDemo {
    public static void main(String[] args) throws IOException {

        //1.创建服务器端
        ServerSocket serverSocket = new ServerSocket(9966);
        System.out.println("服务器连接中");
        //检测是否有客户端连接,成功返回Socket对象
        Socket socket = serverSocket.accept();
        System.out.println("服务器连接成功!开始对话吧");

        //获取输出输出流
        InputStream in = socket.getInputStream();
        OutputStream out = socket.getOutputStream();
        //使用处理流(流中包含其他流)
        DataInputStream din = new DataInputStream(in);
        DataOutputStream dout = new DataOutputStream(out);
        Scanner scanner = new Scanner(System.in);

        while (true){
             //发消息
            System.out.println("向客户端发消息");
            String str1 = scanner.next();
            dout.writeUTF(str1);

            //收消息
            String str2 = din.readUTF();
            System.out.println("新消息:"+str2);
        }
    }
}
```

客户端程序代码:

```java
package networkdemo.tcp;

import java.io.*;
import java.net.Socket;
import java.util.Scanner;

public class ClientDemo {
    public static void main(String[] args) throws IOException {

        /*创建客户段Socket,创建时三次握手,检测网络是否畅通,不畅通报错
        Exception in thread "main" java.net.ConnectException: Connection timed out: connect*/
        //2.创建客户端
        Socket socket = new Socket("127.0.0.1", 9966);
        System.out.println("客户端启动成功,等待对方发");

        InputStream in = socket.getInputStream();
        OutputStream out = socket.getOutputStream();
        //使用处理流(流中包含其他流)
        DataInputStream din = new DataInputStream(in);
        DataOutputStream dout = new DataOutputStream(out);
        Scanner scanner = new Scanner(System.in);

        while (true){
            //收消息
            String str1 = din.readUTF();
            System.out.println("新消息:"+str1);

            //发消息
            System.out.println("向服务器发消息");
            String str2 = scanner.next();
            dout.writeUTF(str2);
        }
    }
}
```

运行时先运行服务器程序ServerDemo,

![在这里插入图片描述](https://img-blog.csdnimg.cn/5fa2c345abce434187b9d88b37deeba7.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


再运行ClientDemo

![在这里插入图片描述](https://img-blog.csdnimg.cn/29d7aa40ea49417fa044c84b6d05459e.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


客户端连接成功后,返回Socket对象,继续执行后续代码

![在这里插入图片描述](https://img-blog.csdnimg.cn/38233b8f65da45078c1cd3a246601d26.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


互向收发消息

服务器:

![在这里插入图片描述](https://img-blog.csdnimg.cn/932747d258534763bdba63c676efea51.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


客户端:

![在这里插入图片描述](https://img-blog.csdnimg.cn/5051b18ac8104842873aba0d46914626.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)

## UDP套接字 (DatagramSocket)
 UDP客户端
主要执行三个步骤。

1. 创建DatagramSocket实例；

2. 使用DatagramSocket类的send()和receive()方法发送和接收DatagramPacket实例；

3. 最后使用DatagramSocket类的close()方法销毁该套接字。
### 发送端

```java
package networkdemo.udp;

import java.io.IOException;
import java.net.*;
import java.util.Scanner;

public class Senter {
    public static void main(String[] args) throws IOException {

        DatagramSocket ds = new DatagramSocket();
        Scanner scanner = new Scanner(System.in);

        while (true){
            System.out.println("发送:");
            String str = scanner.next();
            if(str=="eixt"){
                break;
            }
            byte[] b =str.getBytes("utf-8");
            DatagramPacket dp = new DatagramPacket(b,b.length , InetAddress.getByName("127.0.0.1"),9966 );
            ds.send(dp);
        }
    }
}
```
### 接收端

```java
package networkdemo.udp;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

public class Receiver {
    public static void main(String[] args) throws IOException {

        DatagramSocket ds = new DatagramSocket(9966);
        byte[] b = new byte[1024];

        while (true){
            //将内容封装到数据报中
            DatagramPacket dp = new DatagramPacket(b,b.length);
            //读取内容
            ds.receive(dp);
            String str = new String(b,0,dp.getLength(),"utf-8");
            System.out.println(str);
            //ds.close();
        }
    }
}
```
运行:
①都运行
发送端
![在这里插入图片描述](https://img-blog.csdnimg.cn/380bc03d45434a6b96033d425a4059b4.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)
接收端
![在这里插入图片描述](https://img-blog.csdnimg.cn/9bd00429f7ed4832b3f762b443a178c5.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)
②只运行发送端
![在这里插入图片描述](https://img-blog.csdnimg.cn/5a3d0f23bff74eecb3acb02a9dad6f84.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)

