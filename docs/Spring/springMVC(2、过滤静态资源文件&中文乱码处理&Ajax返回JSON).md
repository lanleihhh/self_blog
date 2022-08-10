
# springMVC(2、过滤静态资源文件&中文乱码处理&Ajax返回JSON)

## 一.过滤静态资源文件

```xml
	<servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
        <!--所有请求都会进入到DispatcherServlet中,不想进入的使用静态资源过滤器-->
    </servlet-mapping>
```

当 DispatcherServlet 的 url 配置为"/"时,所有的请求都会进入DispatcherServlet 中,如.html,.jpg,.js带有后缀名的文件都访问不到,spring提供了`<mvc:default-servlet-handler>`来解决这个问题

在springMVC配置文件中配置 `<mvc:default-servlet-handler>`后,Spring MVC 上下文中定义一个org.springframework.web.servlet.resource.DefaultServletHttpRequestHandler，它会像一个检查员，对进入 DispatcherServlet 的 URL 进行筛查,如果发现是**静态资源的请求**，就将该请求**转由** Web 应用服务器**默认的 Servlet 处理**，如果不是静态资源的请求，才由 DispatcherServlet 继续处理。

```xml
	<!--过滤静态资源文件  以.html,.js,.css为后缀的地址不会进入DispatcherServlet-->
    <mvc:default-servlet-handler></mvc:default-servlet-handler>
```

## 二.中文乱码处理

​		提交请求时,如果输入中文,处理器获取到后可能乱码,在学习servlet时使用过滤器,将有接收请求的servlet添加到一个按指定字符集编码的过滤器中,springMVC中提供了一个过滤器,直接使用即可

在web.xml中配置

```xml
	<!--中文乱码处理~~过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## 三.Ajax返回JSON

在servlet中,我们使用流向前端发送数据,用谷歌的Gson将java实体类转为json格式给前端发送

```java
		PrintWriter out = null;
        //设置响应内容的编码格式
        resp.setContentType("text/html;charset=utf-8");
        if(mark.equals("list")){
            try {
                out = resp.getWriter();
                List<Student> studentList = StudentDao.studentListInfo();
                //集合转为json字符串 "[{id:1,sno:1001},{...},...]"
                out.println(new Gson().toJson(studentList));//返回给前端一个Json字符串
            } catch (Exception e) {
                e.printStackTrace();
                out.println(500);
       		}
```

springMVC中提供了注解标签`@ResponseBody`

- @ResponseBody
  - 将**Controller**的方法返回的对象通过某个转换器转化为指定的格式,写入到到**response** 对象的 **body** 区，通常用来向**处理异步请求,返回 JSON 数据**
  - 注意:在使用此注解之后不会再走视图处理器，而是直接将数据写入到输入流中,效果等同于通过 response 对象输出指定格式的数据

使用@ResponseBody注解标签还须添加一个**jackson**的jar包

```xml
<dependency> 
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId> 
    <version>2.9.1</version> 
</dependency>
```

使用一个登陆示例

### LoginMapper

```java
package com.ffyc.ssm.dao;

import com.ffyc.ssm.model.Admin;
import org.springframework.stereotype.Repository;

@Repository//持久层注解标签
public interface LoginMapper {

    Admin login(Admin admin);
}
```

mybatis配置中定义别名,这样定义一次,以后直接使用model中的类即可

```xml
	<!--为modle包下所有类定义别名-->
	<typeAliases>
        <package name="com.ffyc.ssm.model"/>
    </typeAliases>
```

最初我们一个一个定义:

```xml
	<!--为类定义别名-->
    <typeAliases>
        <typeAlias type="com.ffyc.mybatisdemo.model.Admin" alias="Admin"></typeAlias>
        <typeAlias type="com.ffyc.mybatisdemo.model.Dept" alias="Dept"></typeAlias>
        <typeAlias type="com.ffyc.mybatisdemo.model.Employee" alias="Employee">	</typeAlias>
    </typeAliases>
```

### LoginMapper接口的xml实现

```xml
	<select id="login" parameterType="Admin" resultType="Admin">
        select * from admin where account=#{account} and password=#{password}
    </select>
```

### LoginService


```java
package com.ffyc.ssm.service;

import com.ffyc.ssm.dao.AdminMapper;
import com.ffyc.ssm.dao.LoginMapper;
import com.ffyc.ssm.model.Admin;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service//服务层注解
public class LoginService {

    @Autowired//自动注入
    private LoginMapper mapper;

    @Transactional//事务管理
    public Admin login(Admin admin){
        return mapper.login(admin);
    }
}
```

### LoginController

```java
package com.ffyc.ssm.controller;

import com.ffyc.ssm.common.CommonResult;
import com.ffyc.ssm.model.Admin;
import com.ffyc.ssm.service.LoginService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpSession;

@Controller
@RequestMapping(path = "/login")
public class LoginController {

    @Autowired
    LoginService service;

    /*
       向ajax请求响应数据,
       springmvc添加@ResponseBody后,将return的对象直接转为json(添加jar包)
       后端向前端响应数据时,统一数据格式,封装CommonResult类,封装响应结果 code msg data
     */
    @ResponseBody
    @PostMapping(path = "/login")
    public CommonResult login(Admin admin, HttpSession session){
        CommonResult result = null;
        try{
            Admin admin1 = service.login(admin);
            if(admin1!=null){
                session.setAttribute("admin", admin1);
                result  = new CommonResult<Admin>(200,"登录成功",admin1);
            }else {
                result  = new CommonResult<Admin>(201,"账号/密码错误",admin1);
            }
        }catch (Exception e){
            e.printStackTrace();
            result = new CommonResult<Admin>(500, "登录失败", null);
        }
        return result;
    }
}
```

前端请求

```javascript
		function login(){
            $.post("login/login",$("#loginForm").serialize(),function (res){
                if(res.code==200){
                    alert(res.msg);
                    //存储用户账号信息
                    window.sessionStorage.setItem("account",res.data.account);
                    //存储用户上传文件的新文件名
                    window.sessionStorage.setItem("newFileName",res.data.newFileName);
                    location.replace("success.html");
                }else if(res.code==201||500){
                    alert(res.msg);
                    return;
                }
            },"json")
        }	style="zoom:50%;"
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b14999e29f2f438a9b6ed59762a6bbf1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

