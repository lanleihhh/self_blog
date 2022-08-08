# Java框架--spring(一、hello world)

<font color=#999AAA >前言:Spring 使每个人都可以更快、更轻松、更安全地进行 Java 编程。Spring 对速度、简单性和生产力的关注使其成为世界上最受欢迎的 Java框架 (来自官网)</font>

## 一概述.
<font color=#999AAA > ==Spring== 是于 2003 年兴起的一个**轻量级**的,**IOC**(控制反转)和 **AOP**(面向切面编程) 的 Java 开发框架，它是为了**简化企业级应用开发**而生的。</font>
​		

## 二.spring的优点

1. 轻量级

   -  jar包小,运行消耗内存资源小,核心包小

2. 非侵入式

   -  spring框架代码不会侵入到我们的业务代码(用户管理 servlet service dao) 
   -  不需要继承spring特定的类,通过配置依赖注入后使用类
   - xxServlet extends HttpServlet(侵入式)

3. IOC

   - 我们以前写代码,在哪儿需要一个对象,就地new一个

   -  IOC(**I**nversion **O**f **C**ontrol)，**控制反转** (  把**创建对象的控制权 反转 给spring**) 
   - 由 **Spring IoC** 容器集中统一管理对象,需要用对象时,从容器中拿出来
     	    

4. AOP

   - **A**spect **O**riented **P**rogramming ; 面向切面编程
   - 当在原程序的基础上添加功能时,以前我们需要在源代码中修改(保存日志功能,管理事务commit)
   - AOP的思想:帮我们动态代理调用,而不需要修改源代码

5. 一站式框架

   - 是一个后端管理框架

   - 集**基础容器**,**数据访问**(jdbc,事务管理),**web**(Servlet),**AOP** 为一身

## 三.体系结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/8c5ac59f185941c78a7851f686e8eb27.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)




- Core Container(核心容器): 
  - Beans: 管理 Beans 
  - Core: Spring 核心 
  - Context: 配置文件 
  - ExpressionLanguage: SpEL 表达式

- AOP(切面编程) 
- AOP 框架: Aspects 
- Data Access(数据库整合): 
  - JDBC, ORM, OXM, JMS, Transaction
-  Web(MVC Web 开发):
  - Web, Servlet, Portlet, Struts 

- Test(Junit 整合)

## 四.spring的Hello World搭建
### Hello World1

1. **Maven 导入 spring 核心基础 jar**

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
```

1. **写spring的配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--这是spring配置文件的模板在<beans>里面写配置信息-->
</beans>
```

3. **编写一个实体类**

```java
package com.ffyc.spring.model;

import java.util.List;

public class User {
    private String name;
    private Integer age;

    public User() {
        System.out.println("无参构造被调用了");
    }

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        System.out.println("setName()方法被调用了");
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



4. **在将实体类配置到spring配置文件中**

```xml
<!--配置项目中的类,配置进来后,spring会对其进行管理(生成对象,存储)
        由spring框架生成的对象称为一个bean(特制spring框架生成的对象),它会添加额外的功能-->
<bean id="user" name="user1,user2"  class="User"> </bean>
```

5. **测试spring**

```xml
package test;
import com.ffyc.spring.model.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("springConfig.xml");//ClassPathXmlApplicationContext存放配置实体类的对象的容器
        //User user =  (User) app.getBean("别名");
        User user1 =  (User) app.getBean("user1",User.class);//反射
        User user2 =  (User) app.getBean("user2",User.class);
        System.out.println(user1);
        System.out.println(user2);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/fdf0560c8d194e8ba4bf743a9592067b.png#pic_center)

可以看到执行了一次构造方法,且两个对象地址相同,是同一个对象的引用
### Hello World2

创建一个Dao层的类UserDao

```java
package com.ffyc.spring.dao;

public class UserDao {

    public void save(){
        System.out.println("保存用户信息");
    }
}
```

创建一个服务层的类UserService

```java
package com.ffyc.spring.service;

import com.ffyc.spring.dao.UserDao;

public class UserService {

    private UserDao  userDao;

    public UserDao getUserDao() {
        return userDao;
    }

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void saveUser(){
        userDao.save();//调用UserDao的方法
    }
}
```

在配置文件中管理这两个类

```java
	<!--spring管理了UserDao  UserService-->
    <bean id="userDao" class="com.ffyc.spring.dao.UserDao"></bean>

    <bean id="userService" class="com.ffyc.spring.service.UserService">
        <property name="userDao" ref="userDao"></property>
    </bean>
```

测试

```java
package test;

import com.ffyc.spring.model.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test1 {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("springConfig.xml");//读取配置文件,创建其中管理的对象
       UserService service = app.getBean("userService",UserService.class);//取出UserService对象
                    service.saveUser();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/fbff0d32969b494390834a8d35ccdf92.png#pic_center)


可以看到方法被调用了,我们并没有创建他们的对象,在需要的时候从容器中取出来使用即可
