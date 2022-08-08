


@[TOC](Ajax笔记)


# Ajax

## 概述

 Ajax (Asynchronous JavaScript and XML) 异步 JavaScript 和 XML 

- 使用 Ajax，我们可以无刷新状态更新页面，并且实现异步提交，提升了 用户体验。

- Ajax其实质是利用浏览器提供的一个特殊的对象（XMLHttpRequest） 异步地向服务器发送请求， 

- 服务器返回部分数据，浏览器让你去利用这些数据对页面做部分的更新， 整个过程，页面无刷新，不打断用户的操作 

##  XMLHttpRequest 对象 

- XMLHttpRequest对象：发送请求到服务器并获得返回结果 
- 所有现代浏览器 都内建了 XMLHttpRequest 对象，通过一行简单的 JavaScript 代码，我们就可以创建 XMLHttpRequest 对象。
- new XMLHttpRequest() 

 JavaScript对象XMLHttpRequest是整个Ajax技术的核心，它提供了异步发送请求的能力 

| 方法                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| open(method,URL,async)         | 建立与服务器的连接                                                                                       **method**参数指定请求的HTTP方法,典型的值是GET 或POST                                             **URL**参数指定请求的地址                                                                                                **async**参数指定是否使用异步请求，其值为true或 false |
| send(content)                  | 发送请求 content参数指定请求的参数                           |
| setRequestHeader(header,value) | 设置请求的头信息                                             |

 **常用属性** 

- onreadystatechange：事件，指定回调函数 
- readystate: XMLHttpRequest的状态信息 

| 就绪状态码 | 说明                                       |
| :--------: | ------------------------------------------ |
|     0      | XMLHttpRequest对象没有完成初始化           |
|     1      | XMLHttpRequest对象开始发送请求             |
|     2      | XMLHttpRequest对象的请求发送完成           |
|     3      | XMLHttpRequest对象开始读取响应，还没有结束 |
|     4      | XMLHttpRequest对象读取响应结束             |

-  responseText：获得响应的文本内容 

-  status：HTTP的状态码 

| 状态码 | 说明               |
| ------ | ------------------ |
| 200    | 服务器响应正常     |
| 400    | 无法找到请求的资源 |
| 403    | 没有访问权限       |
| 404    | 访问的资源不存在   |
| 500    | 服务器内部错误     |

## Post/Get方式

- Get方式提交： xmlhttp.open("GET" , "testServlet?name= "+userName,true); 
  - xmlhttp.send(null); 

- Post方式提交 xmlhttp.open("POST" , "testServlet" ,true); 
- POST方式需要自己设置http的请求头 xmlhttp.setRequestHeader("Content-Type" , "application/x- www-form-urlencoded"); 
- POST方式发送数据 xmlhttp.send("name= "+userName);  

## JSON

JSON(JavaScript Object Notation) 是一种轻量级的数据交 换格式  

- 数据在键值对中 
- 数据由逗号分隔 
- 大括号保存对象 
- 方括号保存数组 

语法:

```javascript
<!--JSON 键值对是用来保存 JS 对象的一种方式，和 JS 对象的写法也大同小异，键/值对
组合中的键名写在前面并用双引号 "" 包裹，使用冒号 : 分隔，然后紧接着值：-->
{"firstName": "John"}
{"name":"value","sex":"男"}
```

## Java对象转JSON

在异步交换数据时，java对象不能直接被传递给js,所以需要**先把java对象转换为JSON格式字符串**，把**字符串响应给客户端**，再由**客户端将 JSON字符串转换为js对象**即可（ $.parseJSON(jsonstr) ）。  



## jQuery封装的JSON

 $.post(url,[data],[callback],[type])   若使用get提交,只需post换成get

- url:发送请求地址。 
- data:待发送 Key/value 参数。 
- callback:发送成功时回调函数。 
- type:返回内容格式 : xml, html, script, json, text, _default 

注意: 

- GET请求是通过 URL 提交的 提交有大小限制（2KB）  

- POST 请求是 HTTP 消息实体提交的，提交大小不受限制 



我们使用一个实例来使用jQuery封装的JS

## 注册登录验证

### 1.分别创建登录/验证的页面以及servlet

![在这里插入图片描述](https://img-blog.csdnimg.cn/79e38b31fb6e4be69f3981cf50cecb19.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_15,color_FFFFFF,t_70,g_se,x_16#pic_center)


要实现注册,我们要将前端页面上输入的内容传到后端java代码中,再存入数据库当中,所以我们创建一个dao包专门存放处理数据库与java交互的类(JDBC)

与数据库交互我们还要用到JDBC,在WEB-INF\lib中导入数据库驱动jar包(mysql-connector-java-8.0.16.jar)

同样,我们右击jar包,选择Add as Library,确定



### 2.写一个用户信息表

```mysql
-- 创建存储用户名和密码的数据库 user
CREATE DATABASE IF NOT EXISTS USER CHARSET utf8

-- 创建表
CREATE TABLE user_pwd(
  -- id
  id INT PRIMARY KEY  AUTO_INCREMENT,
  -- 账户名
  username VARCHAR(12) UNIQUE NOT NULL,
  -- 密码
  pwd VARCHAR(12) NOT NULL,
  -- 注册时间
  restime DATETIME
)
```



### 3.在web程序中写一个用户信息类

```java
package com.company.model;
import java.util.Date;
public class User {
    private int id;
    private String account;
    private String password;
    private Date regTime;

    public Date getRegTime() {
        return regTime;
    }

    public void setRegTime(Date regTime) {
        this.regTime = regTime;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getAccount() {
        return account;
    }

    public void setAccount(String account) {
        this.account = account;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

现在我们就可以愉快地使用JDBC与前端进行交互了

### 4.在dao层写JDBC

LoginDao

```java
package com.company.dao;

import com.company.model.User;
import com.google.gson.Gson;

import java.sql.*;

public class LoginDao {

    public static final String URL = "jdbc:mysql://127.0.0.1:3306/USER?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai";
    public static final String USER = "root";
    public static final String PWD = "lanlei6843";
    public static Connection coon = null;//连接
    public static PreparedStatement ps = null;//预编译
    public static ResultSet rs = null;//查询结果
    User user =null;

    //登录--从数据库中查询验证
    public User login(String account,String password) throws ClassNotFoundException, SQLException {
        try {
            //加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //连接数据库
            coon = DriverManager.getConnection(URL,USER,PWD);
            String sql = "SELECT id,username,restime FROM user_pwd where username=? and pwd=?";
            ps = coon.prepareStatement(sql);//预编译
            ps.setString(1, account);
            ps.setString(2, password);
            //执行查询操作
            rs = ps.executeQuery();//将查询到的结果返回给resultSet集
            if(rs.next()){
                user = new User();
                user.setAccount(rs.getString("username"));
                user.setId(rs.getInt("id"));
                user.setRegTime(rs.getTimestamp("restime"));
            }
        }finally {
            if(coon!=null){
                coon.close();
            }
            if(ps!=null){
                ps.close();
            }
            if(rs!=null){
                rs.close();
            }
        }
        return user;
    }
}
```

RegisterDao

```java
package com.company.dao;

import com.company.model.User;

import java.sql.*;

public class RegisterDao {
    public static final String URL = "jdbc:mysql://127.0.0.1:3306/USER?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai";
    public static final String USER = "root";
    public static final String PWD = "lanlei6843";
    public static Connection coon = null;//连接
    public static PreparedStatement ps = null;//预编译
    public static ResultSet rs = null;//查询结果

    //注册--往数据库添加
    public void register(String account,String password) throws SQLException, ClassNotFoundException {
        try {
            //加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //连接数据库
            coon = DriverManager.getConnection(URL,USER,PWD);
            String sql = "INSERT INTO user_pwd(username,pwd,restime)\n" +
                    "\tVALUES(?,?,NOW());";
            ps = coon.prepareStatement(sql);//预编译
            ps.setString(1, account);
            ps.setString(2, password);
            //执行更新操作
            ps.executeUpdate();
        }finally {
            if(coon!=null){
                coon.close();
            }
            if(ps!=null){
                ps.close();
            }
        }
    }

    //检查数据库中是否有这个账号
    public int CheckRepeat(String account) throws ClassNotFoundException, SQLException {
        int res = 0;
        try {
            //加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //连接数据库
            coon = DriverManager.getConnection(URL,USER,PWD);
            String sql = "SELECT count(*) FROM user_pwd WHERE username=?";
            ps = coon.prepareStatement(sql);//预编译
            ps.setString(1, account);
            //执行查询操作
            rs =  ps.executeQuery();
            if(rs.next()){
                res = rs.getInt(1);//取出第一行第一列查询结果集中的统计个数
            }
        }finally {
            if(coon!=null){
                coon.close();
            }
            if(ps!=null){
                ps.close();
            }
            if(rs!=null){
                rs.close();
            }
        }
        return res;
    }
}
```

### 5.重写Servlet的doPost()方法,处理前端传入的数据

注册

```java
package com.company.servlet;

import com.company.dao.RegisterDao;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class RegisterServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp){
        PrintWriter out =null;
        try {
            out = resp.getWriter();
            //设置post请求数据解码格式  解码格式必须在接收数据之前
            req.setCharacterEncoding("utf-8");
            //设置响应内容的编码格式
            resp.setContentType("text/html;charset=utf-8");
            RegisterDao dao = new RegisterDao();
            //通过前端标签的name值获取value
            String account = req.getParameter("account");
            String password = req.getParameter("password");
            dao.register(account,password);
            out.println(666);//666:成功,444:失败
        } catch (Exception e) {
            e.printStackTrace();
            out.println(444);//失败
        }
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter out =null;
        try {
            out = resp.getWriter();
            //设置响应内容的编码格式
            resp.setContentType("text/html;charset=utf-8");
            RegisterDao dao = new RegisterDao();
            String account = req.getParameter("account");
            int res  = dao.CheckRepeat(account);
            out.println(res);//0未注册,非0已注册,444:异常
        } catch (Exception e) {
            e.printStackTrace();
            out.println(444);//异常
        }
    }
}
```

登录

```java
package com.company.servlet;

import com.company.dao.LoginDao;
import com.company.dao.RegisterDao;
import com.company.model.User;
import com.google.gson.Gson;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter out =null;
        String str = null;
        out = resp.getWriter();
        try {
            //设置post请求数据解码格式  解码格式必须在接收数据之前
            req.setCharacterEncoding("utf-8");
            //设置响应内容的编码格式
            resp.setContentType("text/html;charset=utf-8");
            LoginDao loginDao = new LoginDao();
            //通过前端标签的name值获取value
            String account = req.getParameter("account");
            String password = req.getParameter("password");
            User user = loginDao.login(account,password);//返回User对象

            if(user!=null){
                Gson gson = new Gson();
                str = gson.toJson(user);//将user对象转为json字符串
                out.println(str);//str:成功
            }else {
                out.println(444);
            }
        } catch (Exception e) {
            e.printStackTrace();
            out.println(str);//失败
        }
    }
}
```



### 6.在前端接收后端返回的结果

#### 未封装版

注册

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册</title>

    <script src="js/jquery-1.8.3.min.js"></script>
    <script type="text/javascript">
        function registerSubmit(){
            var account = document.getElementById("accountId").value;
            var password = document.getElementById("passwordId").value;
            //XMLHttpRequest对象:发送请求到服务器并获得返回结果
            var httpobj = new XMLHttpRequest();
            //建立与服务器的连接
            httpobj.open("post","RegisterServlet",true);
            //设置请求头信息
            httpobj.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
            //发送
            httpobj.send("account="+account+"&password="+password);
            httpobj.onreadystatechange=function (){
                if(httpobj.readyState==4&&httpobj.status==200) {
                    if (httpobj.responseText == 666) {
                        alert("恭喜,注册成功");
                        location.replace("index.html");
                    }else {
                        alert("注册失败");
                    }
                }
            }
        }
    </script>
</head>
<body>
<form id="formId">
    账号:<input type="text" id="accountId" name="account" value=""><br>
    密码:<input type="password" id="passwordId" name="password" value=""><br>

    <input type="button" value="注册" onclick="registerSubmit()">
    <a href="index.html">返回登录</a>
</form>
</body>
</html>
```

登录

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>firstwebpro</title>
    <script src="js/jquery-1.8.3.min.js"></script>
    <script type="text/javascript">
        //提交登录信息给后端验证
        function loginSubmit(){
            var account = document.getElementById("accountId").value;
            var password = document.getElementById("passwordId").value;
            //XMLHttpRequest对象:发送请求到服务器并获得返回结果
            var httpobj = new XMLHttpRequest();
            //建立与服务器的连接
            httpobj.open("post","LoginServlet",true);
            //设置请求头信息
            httpobj.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
            //发送
            httpobj.send("account="+account+"&password="+password);
            httpobj.onreadystatechange=function (){
                if(httpobj.readyState===4&&httpobj.status==200) {
                    //从后端响应回来一个json格式的字符串,在前端需要将其转为js对象,方便前端操作
                    var obj = $.parseJSON(httpobj.responseText);
                    //console.log(obj);
                    if (obj.id!=null) {
                        alert("登录成功");
                        location.replace("success.html");
                    } else if (httpobj.responseText == 444) {
                        alert("账户或密码错误");
                    } else {
                        alert("网络出错");
                    }
                }
            }
        }
    </script>
</head>
<body>

    <!--
        method不写,默认为get请求方式
        action:要提交的地址
     -->
    <form id="loginFormId">
        账号:<input type="text" id="accountId" name="account" value=""><br>
        密码:<input type="password" id="passwordId" name="password" value=""><br>

        <input type="button" value="登录" onclick="loginSubmit()">
        没有账号?点击<a href="register.html">注册</a>
    </form>
</body>
</html>
```



#### jQuery封装的JSON版本

登录页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>firstwebpro</title>
    <script src="js/jquery-1.8.3.min.js"></script>
    <script type="text/javascript">
        //提交登录信息给后端验证
        function loginSubmit(){
            $.post("LoginServlet",$("#loginFormId").serialize(),function (res){
                var obj = $.parseJSON(res);
                if (obj.id!=null) {
                    alert("登录成功");
                    location.replace("success.html");
                } else if (res == 444) {
                    alert("账户或密码错误");
                } else {
                    alert("网络出错");
                }
            });

        }
    </script>
</head>
<body>

    <!--
        method不写,默认为get请求方式
        action:要提交的地址
     -->
    <form id="loginFormId">
        账号:<input type="text" id="accountId" name="account" value=""><br>
        <span style="color: red" id="spanId"></span><br>
        密码:<input type="password" id="passwordId" name="password" value=""><br>

        <input type="button" value="登录" onclick="loginSubmit()">
        没有账号?点击<a href="register.html">注册</a>
    </form>
</body>
</html>
```

注册页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册</title>

    <script src="js/jquery-1.8.3.min.js"></script>
    <script type="text/javascript">
        function registerSubmit(){
            $.post("RegisterServlet",$("#registerFormId").serialize(),function (res){
                if (res==666) {
                    alert("注册成功");
                    location.replace("index.html");

                }else {
                    alert("网络出错");
                }
            });
        }

        function checkRepeat(account){
            $.get("RegisterServlet",{account:account},function (res){
                if(res>0){
                    $("#spanId").html("该账号已被注册");
                    return;
                }else if(res==0){
                    $("#spanId").html("恭喜,账号可用");
                }else{
                    alert("网络异常");
                }
            });
        }
    </script>
</head>
<body>
<form id="registerFormId">
    账号:<input type="text" id="accountId" name="account" onblur="checkRepeat(this.value)"><br>
    <span style="color: red" id="spanId"></span><br>
    密码:<input type="password" id="passwordId" name="password" value=""><br>

    <input type="button" value="注册" onclick="registerSubmit()">
    <a href="index.html">返回登录</a>
</form>
</body>
</html>
```

登录成功页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录成功</title>
</head>
<body>
    <h1>登录成功</h1>
</body>
</html>
```

测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/774b84066ca34764a03571947e29bc8c.gif#pic_center)

