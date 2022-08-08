@[TOC](Servlet笔记上)
# servlet

## 1.web开发概述



web程序

所有的程序都部署在服务器端,通过浏览器远程访问

服务器(中间件)

 Web服务器是指驻留于因特网上某种类型计算机的**程序**,可以向浏览器等Web客户端提供文档，也可以放置网站文件，让全世界浏览； 它是一个**容器**，是一个连接用户与程序之间的**中间件** 

提供服务,接收请求, 相应

放置文件(项目),供因特网中其他 访问

- 广义:

**服务器=软件+硬件**

- 狭义:

**服务器=软件(程序)**   放置文件(项目) 供因特网中的其他电脑访问

**服务器**-->**web容器**-->**中间件**(连接用户请求与程序)

- 学习web开发，需要先安装一台web服务器，将开发好的web项目 部署在web服务器中供外界访问,我们使用 Apache 提供的免费的Web服务器--Tomcat, 该服务器支持全部JSP(已淘汰)以及Servlet规范 

![在这里插入图片描述](https://img-blog.csdnimg.cn/fc91295e0cff40f799ec1c44cbecf119.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




## 2.web开发环境搭建

安装服务器 apache提供的一款开源服务器 TomCat (汤姆猫)

步骤

①官网下载

 Tomcat官方站点：http://tomcat.apache.org 

![在这里插入图片描述](https://img-blog.csdnimg.cn/ad32c92dba6c438499056620d16913cb.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


 ②获取Tomcat安装程序包 

- tar.gz文件是Linux操作系统下的安装版本 

- zip文件是Windows系统下的压缩版本 

![在这里插入图片描述](https://img-blog.csdnimg.cn/8c679aeacab0483facb4dd1fdc11ee77.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




③安装JDK 

- 设置JAVA_HOME环境变量 

变量名:**JAVA_HOME**

变量值:jdk的安装目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/babb5c170e4947f085df3ed02f7a04ca.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


- 设置PATH环境变量 



安装Tomcat 

- 解压tomcat压缩文件  

Tomcat的目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/5792f100ff084da9bf6628bc008b9862.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


在Tomcat的**bin**目录中,存放着各种命令

- startup.bat启动服务器
- shutdown.bat关闭服务器

![在这里插入图片描述](https://img-blog.csdnimg.cn/b2f9722daea84470962e84fa40374196.jpg#pic_center)


双击startbat,打开Tomcat后,输入:

-  http://localhost:8080  或
-  http://127.0.0.1:8080  或
- 局域网IP:端口号

访问Tomcat

![在这里插入图片描述](https://img-blog.csdnimg.cn/7e50a1a5ac9a4b82928ababf1575e50c.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


若能正常访问该页面,表示Tomcat的环境变量已经没有问题了

server.xml是Tomcat的主配置文件提供Tomcat组件的初始配置，tomcat启动的时候执行这些初始化设置。 修改此文件后，需要重新启动tomcat。如果此文件修改错误，则tomcat不能正常启动 

- 修改服务器端口号
![在这里插入图片描述](https://img-blog.csdnimg.cn/e034c918cc8f474ca67a9c09a5853914.jpg#pic_center)
修改后我们继续访问Tomcat本地服务器
![在这里插入图片描述](https://img-blog.csdnimg.cn/01f3ea37183244f690f08c1ce9ba6880.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)





## 3.创建并发布Web项目

我们使用idea创建web项目

第一步创建普通java项目,右击项目,选择add 

![在这里插入图片描述](https://img-blog.csdnimg.cn/8e4182f1b7e84391b29d45cb0b6d5427.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


第二步,创建一个hello world网页

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>firstwebpro</title>
</head>
<body>
    <a href="">This is my first web project</a>
</body>
</html>
```

第三步,在idea中配置Tomcat

 1点击add Configuartions

![在这里插入图片描述](https://img-blog.csdnimg.cn/54d35713aa7542699e64fb46468ab684.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


2点击Deployment

![在这里插入图片描述](https://img-blog.csdnimg.cn/2013aee621794232a2e460956dcf6452.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


3.

![在这里插入图片描述](https://img-blog.csdnimg.cn/26084e8b631b4c3c93508b0ed65c056e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


4.显示如下,配置完成

![在这里插入图片描述](https://img-blog.csdnimg.cn/231b8fd7757c4464ba5f0e7b41a840b7.png#pic_center)


我们现在来运行一下我们的web程序

![在这里插入图片描述](https://img-blog.csdnimg.cn/3b03fad5f78f4e78bc11ba6f5ab517c0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)


只要本地的Tomcat服务器运行,我们在工具中修改代码,浏览器刷新就会自动更新,而不用写完再重新部署到服务器那么麻烦,==**适合用于开发测试期间**==

## 4.Servlet

### 概述

Servlet(Server Applet)也就是我们所说的JavaEE或者JavaWeb程序

- 即用Java编写的服务器端程序
- **运行在服务器**中,服务器负责servlet与用户之间的通行请求
- Servlet和用户通信采用:**==请求==**/**==响应==**的模式

Servlet作用:

- 接收客户端/浏览器发送的请求
- 调用Java代码处理请求
- 服务器根据处理结果响应请求

### Servlet的创建与使用

#### 1 导包:servlet.api.jar

1. 在web/WEB-INF的目录中创建一个lib目录,导入运行Servlet所需要的包,我们从Tomcat的安装目录的lib里面找到servlet.api.jar,复制到WEB-INF/lib/

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/0bb3972509094e328b6425ced7da836c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)






右击servlet.api.jar,选择Add as Library,点击确定

![在这里插入图片描述](https://img-blog.csdnimg.cn/43370d14974f4cb9981825c9ed72e0c8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


#### 2.自定义类 extends  HttpServlet ( javax.servlet.http) 

创建一个类继承 javax.servlet.http包下的HttpServlet类

```java
package com.company.servlet;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class ServletDemo extends HttpServlet {
    /**
     * 构造方法
     * 只被执行一次,只创建一个对象,此对象由服务器创建
     * 默认是在客户端第一次访问该servlet时,创建servlet对象
     *<load-on-startup>0</load-on-startup> 值>=0 时,会在服务器自动创建
     */
    public ServletDemo() {
        System.out.println("ServletDemo构造");
    }

    /**
     * 初始化
     * 当servlet对象创建后,服务器会自动调用init()方法,完成一些初始化操作
     * 如果我们没有需要初始化的操作,也可以不重写init(),但是服务器依然会调用(父类重写的init())
     */
    @Override
    public void init(ServletConfig config) throws ServletException {
        System.out.println("init初始化");
    }

    /**
     * 服务响应
     * 为请求提供服务,每一次http请求,都会调用service(请求,响应)
     */
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("Service开始服务");
    }

    /**
     * 销毁
     * 当servlet对象销毁时,服务器会自动调用destroy()
     * 完成一些最终的操作(如保存日志)
     * 如果我们没有一些最终的操作,也可以不重写destroy(),但是服务器依然会调用父类中的destroy
     */
    @Override
    public void destroy() {
        System.out.println("destroy销毁");
    }
}
```

#### 3.在web.xml文件中配置Servlet 

语法:

```xml
<!--配置servlet 把servlet配置进来,服务器就知道有这么一个类-->
<servlet>
    <!-- 为servlet对象定义一个名称 -->
    <servlet-name>注册名称</servlet-name>
    <servlet-class>完整类名</servlet-class>
    <!-- 配置类的地址,让服务器使用反射机制(反序列化)创建对象 -->
</servlet>

<!--为servlet配置一个供前端访问的映射地址-->
<servlet-mapping>
    <servlet-name>注册名称</servlet-name>
    <!--地址以"/"开头,/表示项目的根目录 http://localhost:63342/firstweb/web/index.html?_ijt=11aalqdggu3pqq8b9j3p07p53u-->
    <url-pattern>对外访问路径</url-pattern>
</servlet-mapping>
```

为创建的Servlet配置信息

```xml

<!-- xml:属于标记语言,xml是可扩展标记语言 用来保存数据 保存配置文件数据
	 html是标记内容,指导内容如何显示
	 web.xml: 是web程序的配置文件,保存各种配置信息,在服务器启动时,由服务器读取
-->

<!--配置servlet 把servlet配置进来,服务器就知道有这么一个类-->
<servlet>
    <servlet-name>ServletDemo</servlet-name>
    <servlet-class>com.company.servlet.ServletDemo</servlet-class>
</servlet>

<!--为servlet配置一个供前端访问的映射地址-->
<servlet-mapping>
	<servlet-name>ServletDemo</servlet-name>
	<url-pattern>/ServletDemoURL</url-pattern>
</servlet-mapping>
```

配置完Servlet后,我们对网页文件稍加修改,将映射地址放到超链接的地址中

```html
<a href="ServletDemoURL">This is my first web project</a>
```

#### 4.运行Servlet程序 

然后我们运行程序

![在这里插入图片描述](https://img-blog.csdnimg.cn/c65f2920e3ae4770aeeb6e244e8a6f58.gif#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/1aee5d0756434aa29cf87ac1be662365.gif#pic_center)


Servlet的生命周期

![在这里插入图片描述](https://img-blog.csdnimg.cn/b6e76a32836449288f54dcf5669f49e3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


在配置文件web.xml中,给`<servlet>`加一些属性

```xml
<!--配置初始化时信息-->
<init-param>
    <param-name>name</param-name>
    <param-value>JavaWebApp</param-value>
</init-param>

<!-- 配置servlet何时被加载创建,默认第一次访问,值>=0 服务器启动就可以创建 -->
<load-on-startup>0</load-on-startup>
```

在初始化方法中获取配置

```java
@Override
public void init(ServletConfig config) throws ServletException {
    System.out.println("init初始化");
    System.out.println("配置文件init的name:"+config.getInitParameter("name"));//获得配置文件中的信息
    System.out.println("配置文件中Servlet名称:"+config.getServletName());
}
```

运行程序:

![在这里插入图片描述](https://img-blog.csdnimg.cn/431ddf7b94ff42c2b5e2cd94a179b0e4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




## 5.HTTP协议

 什么是HTTP？ 

- **超文本传输协议**（HyperText Transfer Protocol）服务器传输超文本到本地浏览器的传送协议,是互联网上应用最为流行的一种网络协议,用于定义**客户端浏览器和服务器之间交换数据**的过程。 

- HTTP是一个**基于TCP/IP**通信协议来传递数据.

- HTTP是一个**属于应用层的协议**，由于其简捷、快速的方式，适用于分布式超媒体信息系统 

### HTTP请求

 http请求？ 

- **客户端连上服务器后**，向服务器**请求**某个**web资源**，称之为客户端向服务器**发送**了一个**http请求**。 
-  请求包括: **请求行**，**请求头**，**请求体** 

- 请求行: 包含http请求方式，请求资源名称，http版本 (例如post test.jsp HTTP/1.1)  



- 请求头:

  包含: 主机地址，以及客户端的一些环境信息，以键值对的形式传递 

![在这里插入图片描述](https://img-blog.csdnimg.cn/b242771839e145b5bce2f1c82cdff8e3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


- 请求体: 浏览器在post请求方式中传递给服务器的参数,请求体中参数以键值形式传递,多个用&连接接，服务器接收到后再解析

   username=admin&userpwd=123  

### 两种http请求方式

GET:

- 超链接访问，默认是GET方式  

- form提交，不指定method，默认为GET方式 

POST:

- form提交，指定method=“POST” 

GET与POST的区别

- **Get**方式主要是从服务器**获取**信息；==post==主要是想服务器==提交==信息 
- **Get**方式在通过URL**提交**数据，数据在URL中**可以看到**；==POST方式==，数据放置在==请求体中==提交,在URL中==看不见==。
- **GET**方式提交的数据大小**受限**制一般1kb(不同浏览器也会有不同);而==POST==则==没有==此限制 

## 6.Request&Response

**HttpServletRequest**

- HttpServletRequest是ServletRequest的子接口 
- HttpServletRequest比ServletRequest多了一些针对于Http协议的方法
- 请求和响应是Web交互最基本的模式，在Servlet中，用HttpServletRequest 来表示请求
- HttpServletRequest ：封装了请求的信息，可以从中获取任何请求信息 

**HttpServletResponse**

- HttpServletResponse是ServletResponse的子接口 。 
- Web服务器收到客户端的http请求，会针对每一次请求，分别创建一个代表响应的HttpServletResponse对象
- HttpServletResponse对象代表响应 

| 方法                               | 说明                         |
| ---------------------------------- | ---------------------------- |
| getMethod()                        | 得到客户机请求方式           |
| getScheme()                        | 请求协议                     |
| getRemoteAddr()                    | 返回发出请求的客户机的IP地址 |
| getServerName()                    | 服务器名(ip或域名)           |
| getServerPort()                    | 服务器端口                   |
| String getParameter(name)          | 通过name获得值               |
| String[]  getParameterValues(name) | 通过name获得多值             |

我们修改一下index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>firstwebpro</title>
</head>
<body>
    <!--
        method不写,默认为get请求方式
        action:要提交的地址
     -->
    <form action="ServletDemoURL" method="">
        账号:<input type="text" name="account" value="">
        密码:<input type="password" name="password" value="">
        <input type="submit">
    </form>
</body>
</html>
```

再修改一下Servlet,重写doGet()方法

```java
package com.company.servlet;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class ServletDemo extends HttpServlet {
    /**
 * 从浏览器中以超链接,表单等向服务器发送的请求都称为HTTP请求
 *     请求行,请求头(这两部分由浏览器自动发送),请求体
 *     请求行:包含请求地址URL,请求方式post/get,协议版本(HTTP1.1)
 *     请求体:指表单post方式请求时,向服务器端发送的数据
 *
 *     get与post的区别:
 *     get: 从服务器端获取数据,也可以发送(在地址栏会显示发送的数据,不安全,传输的数据大小受限,一般浏览器大小在2~5kb)
 *     post:向服务器端发送数据,发送的数据在请求体中,相比与get更为安全,传输数据大小不受限
 *     我们最常重写的就是doPost()和doGet(),而不重写service()等方法
 *     service()方法中调用了doPost和doGet,请求数据时调用父类的service()
 *     
 *     doGet():处理get请求
 *     tomcat8.0以后,get请求数据的编码格式支持中文
 */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
        //请求行数据
        System.out.println("请求方式:"+req.getMethod());
        System.out.println("协议:"+req.getProtocol());
        System.out.println("服务器端口:"+req.getServerPort());
        System.out.println("服务器名称:"+req.getServerName());
        //请求头数据
        System.out.println("客户端IP:"+req.getRemoteAddr());
        System.out.println("客户端端口:"+req.getRemotePort());
        System.out.println("使用的浏览器和操作平台信息:"+req.getHeader("User-Agent"));
        //请求体数据
        System.out.println("账号:"+req.getParameter("account"));
        System.out.println("密码:"+req.getParameter("password"));
    }
}
```

运行程序:

![在这里插入图片描述](https://img-blog.csdnimg.cn/adeea3d4b69d473fa20c59b6760a62a5.gif#pic_center)




## 7.向浏览器动态生成网页内容 



- 用**getWriter()**获得一个PrintWriter字符输出流输出数据 

- response会默认以ISO8859-1将需要输出到浏览器的字符进行解码， 如果输出的字符在ISO8859-1中不存在，就会导致乱码问题。 

- response.setContetnType("**text/html;charset=utf-8**");方法可以同时设定response所使用的字符集编码和浏览器所用的字符集编码 



修改表单中提交方式method为post方式,并添加复选框

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>firstwebpro</title>
</head>
<body>

    <!--
        method不写,默认为get请求方式
        action:要提交的地址
     -->
    <form action="ServletDemoURL" method="post">
        账号:<input type="text" name="account" value="">
        密码:<input type="password" name="password" value="">
        <!-- value中的值是发送到服务器的,后面的只是显示在浏览器中,为了区分,我们给value加个v -->
        爱好:<input type="checkbox" name="ck" value="打球v">打球
            <input type="checkbox" name="ck" value="听歌v">听歌
            <input type="checkbox" name="ck" value="学习v">学习
        	<input type="checkbox" name="ck" value="睡觉v">睡觉
        <input type="submit">
    </form>
</body>
</html>
```

在Servlet中重写doPost()方法

```java
/*
    doPost():
    post请求数据编码格式不支持中文,若不设置其编码格式,会乱码
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp){
        System.out.println("doPost");
        PrintWriter out = null;//字符输出流
        try {
            //设置请求内容的解码格式  解码格式必须在接收数据之前
            req.setCharacterEncoding("utf-8");
            //接收数据  name获得值
            System.out.println("账号:"+req.getParameter("account"));
            System.out.println("密码:"+req.getParameter("password"));
            //name获得一组值
            System.out.println("复选框选择:"+Arrays.toString(req.getParameterValues("ck")));
            //设置响应内容的编码格式
            resp.setContentType("text/html;charset=utf-8");
            out = resp.getWriter();
            out.println("<h1>登录成功</h1>");
        } catch (Exception e) {
            e.printStackTrace();
            out.println("<h1>服务器繁忙...</h1>");
        }
    }
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/26def81017e74868b6c2b2d2ca23f235.gif#pic_center)








## 8.状态码

状态码：服务器和浏览器用于确定状态的固定数字号码 

- 200：请求成功 
- 302：请求重定向 
- 400：语义有误，当前请求无法被服务器理解或请求参数有误 
- 404：请求资源不存在，通常是路径写错了或者服务器资源删除了 
- 500：服务内部错误（代码异常） 

查看更多状态码:https://www.runoob.com/http/http-status-codes.html



