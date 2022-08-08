

## Tomcat管理文件夹

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed5492ca727244a59d1484a25f330e99.png#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/561aa9785c2b471fba7b9996a0d66639.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




![在这里插入图片描述](https://img-blog.csdnimg.cn/64f13c5f849d4ac5908a64ebcec4e184.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center )




## 文件上传

导入上传下载所需 jar 包(pom.xml)

```xml
<dependency> 
    <groupId>commons-fileupload</groupId> 
    <artifactId>commons-fileupload</artifactId> 
    <version>1.3.3</version> 
</dependency>
```

文件解析器(springMVC.xml)

```xml
 <!--文件解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="utf-8"></property>
        <property name="maxUploadSize" value="10485760"></property>
        <!--10485760 10兆-->
    </bean>
```

分析文件上传的流程

需求：登录账户可以上传一个头像，下次登录时依旧显示

1. 登录账号，点击上传头像（`<input type="file" accept=".jpg,.png,.gif">`）

2. 使用$.ajaxFileUpload将文件提交到服务器(最初使用ajax异步请求,非常麻烦)

3. 在Controller(控制层)中接收文件--使用apache的组件+springMvc进行封装,简化接收操作

   apache组件将接收的二进制信息转为文件,springMVc对这个过程进一步封装

4. 将接收到的文件,存储到服务器中 [admin用户名 123.gif(旧图片名)    20211125164145195.gif(生成的新图片名)]
5. 在数据库中将文件与用户绑定
6. 登录时根据文件与用户的关系,显示出头像

文件上传接口

```java
package com.ffyc.ssm.dao;

import com.ffyc.ssm.model.Admin;
import org.springframework.stereotype.Repository;

@Repository
public interface AdminMapper {
	
	//文件上传
    void saveAdminIcon(Admin admin);
}
```

文件上传xml实现

```xml
	<!--上传文件,绑定与用户的关系-->
    <update id="saveAdminIcon" parameterType="Admin">
        update admin set new_file_name=#{newFileName},
                         old_file_name=#{oldFileName}
                        where id=#{id}
    </update>
```



文件上传后端方法

```java
package com.ffyc.ssm.controller;

import com.ffyc.ssm.common.CommonResult;
import com.ffyc.ssm.model.Admin;
import com.ffyc.ssm.service.AdminService;
import com.ffyc.ssm.util.StringUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.commons.CommonsMultipartFile;

import javax.servlet.http.HttpSession;
import java.io.File;
import java.io.IOException;

@Controller
@RequestMapping(path = "/admin")
public class AdminController {

    @Autowired
    AdminService service;
    
    /*文件上传
        apache组件+springMVC封装 接收组装的文件
        @Param("fileName") CommonsMultipartFile file
     */
    @PostMapping(path = "/fileUpload")
    @ResponseBody
    public CommonResult upload(@RequestParam("fileName") CommonsMultipartFile file, HttpSession session){

        CommonResult<Admin> res = null;
        //拿到session中的登录信息
        Admin admin = (Admin)session.getAttribute("admin");
        //System.out.println(file.getOriginalFilename());//获得原始文件名
        String filePath = "D:\\userImg\\"+admin.getAccount();
        System.out.println("filePath:"+filePath==null);
        File f = new File(filePath);
        if(!f.exists()){//判断是否存在此文件
            f.mkdir();//若不存在创建
        }

        String oldFileName = file.getOriginalFilename();//上传的文件名
        String newFileName = StringUtil.newFileName(oldFileName);//生成新的文件名
        File destFile = new File(f,newFileName);//用新的文件名生成文件
        try {
            file.transferTo(destFile);//将文件输出到指定目录中

            //存储用户与文件的关系
            Admin admin1 = new Admin();
                admin1.setId(admin.getId());
                admin1.setOldFileName(oldFileName);
                admin1.setNewFileName(newFileName);
            service.saveAdminIcon(admin1);//保存新旧文件名
            res = new CommonResult<>(200, "上传成功", admin1);
        } catch (IOException e) {
            e.printStackTrace();
            res = new CommonResult<>(500, "上传失败", null);
        }
        return res;
    }
}
```

文件上传前端请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/jquery-1.8.3.min.js" type="text/javascript"></script>
    <script src="js/ajaxfileupload.js" type="text/javascript"></script>

    <script type="text/javascript">
        function fileUpload(){
            $.ajaxFileUpload({
                url: 'admin/fileUpload', //用于文件上传的服务器端请求地址  调用后端接口(文件上传的方法)
                fileElementId: 'fileId', //文件上传域的ID
                dataType: 'json', //返回值类型 一般设置为json
                success: function (data){//success:回调函数 ==$.post("",function(res){})
                    console.log(data);
                    if(data.code==200){
                        alert("上传成功");
                        //拿到session中登录账户
                        var account =  window.sessionStorage.getItem("account");
                        //拿到Tomcat代管的userImg下的文件名
                        var iconSrc = "http://localhost:8888/userImg/"+account+"/"+data.data.newFileName;
                        //将上传的图片显示到浏览器
                        window.parent.document.getElementById("iconId").innerHTML="" +
                            "<img src='"+iconSrc+"' width='50px' height='50px'>";
                        console.log(iconSrc);
                    }else{
                        alert("上传失败");
                    }
                }
            })
        }

    </script>
</head>

<body>

    请选择一个文件
    <input type="file" name="fileName" id="fileId" accept=".jpg,.png,.gif">
    上传
    <input type="button" value="上传" onclick="fileUpload()">

</body>
</html>
```
示例
![在这里插入图片描述](https://img-blog.csdnimg.cn/23a8353fcade47eeabb20e435a2added.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 拦截器

​	类似于servlet中的过滤器**Filter**

​	

​		SpringMVC 定义了拦截器接口 **HandlerInterceptor** ,该接口中定义了三个方法,这三个方法的调用时在 SpringMVC 框架内部完成的， 调用这个三个方法的时候，其参数的值也是从框架内部传递进来的。

- `boolean preHandle`

  进入到控制器之前执行   返回false 不会进入拦截器

- `void postHandle`

  控制器执行完成后再执行拦截器

- `void afterCompletion`

  整个请求结束后再执行

拦截器实现

 .编写一个类，继承 HandlerInterceptorAdapter

```java
package com.ffyc.ssm.util;

import com.ffyc.ssm.model.Admin;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

public class LoginInterceptor implements HandlerInterceptor {
    /*
    * 预处理: 进入到控制器之前执行
    * 返回false 不会进入控制器
    * 返回true  继续向下执行,到达下一个拦截器,或控制器
    * */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        Admin admin = (Admin) session.getAttribute("admin");
        if(admin==null){
            response.getWriter().print(202);//登录失效
            return false;
        }else {
            return true;
        }
    }

    /*//控制器执行完成后再执行拦截器
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    //整个请求结束后再执行
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
    }*/
}
```

在springMVC的配置文件中配置拦截器

```xml
	<!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!-- /**  所有请求都进入拦截器 -->
            <mvc:mapping path="/**"/>
            <!-- exclude-mapping: 不进入拦截器的请求 -->
            <mvc:exclude-mapping path="/login/login"/>
            <mvc:exclude-mapping path="/js/**"/>
            <mvc:exclude-mapping path="/css/**"/>
            <mvc:exclude-mapping path="/img/**"/>
            <mvc:exclude-mapping path="/**.html"/>
            <bean id="loginInterceptor" class="com.ffyc.ssm.util.LoginInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```














