




# 一、AOP

## 1.AOP概述

​		**AOP**(**A**spect **O**riented **P**rogramming)面向**切面**编程,其中Aspect 意为切面.AOP是OOP( Object Oriented Programming 面向对象编程)的延续.利用 AOP 可以**对业务逻辑**的各个部分进行**隔离**,从而使得业务逻辑各部分之间的**耦合度降低**，**提高**程序的**可重用性**，同时**提高**了开发的**效率**。

AOP与OOP的区别:

- **OOP**:面向对象编程,针对业务处理过程的实体及其**属性**和**行为**进行**抽象封装**，以获 

  得更加清晰高效的**逻辑单元划分**

- **AOP**:针对业务处理过程中的切面进行提取，它所面对的是处理过程中的**某个步骤或阶段**，以获得逻辑过程中

  各部分之间低耦合性的**隔离**效果。这两种设计思想在目标上有着本质的差异

- **OOP**是整体设计,**AOP**是针对业务代码的公共部分进行抽取

- 面向切面编程只是面向对象编程的一种补充

**使用AOP的好处**:减少重复,专注业务

==AOP的核心原理==:

- 使用**动态代理**的方式在**执行方法前后**或者**出现异常**的时候做**加入相关的逻辑**
- 可以实现在不修改代码的情况下,为程序添加新的功能
- 抽取的代码也是有一定条件(功能与业务逻辑没有直接关系)

## 2.AOP 的基本概念

**连接点（Joinpoint）**：类中可以被增强(在原来基础上增加功能)的方法，这个方法就被称为连接点 

**切入点（pointcut）**：类中有很多方法可以被增强，但实际中只有 add 和 update被增了，那么 add 和 update 方法就被称为切入点（实际实现的连接点） 

**通知(Advice):** 

- 通知是指一个切面在特定的连接点要做的事情(增强的功能)。

- 通知分为方法执行前通知，方法执行后通知，环绕通知等. 

**切面(Aspect)**:把通知添加到切入点的过程叫切面. 

**目标(Target)**: 代理的目标对象(要增强的类) 

**代理(Proxy)**: 向目标对象应用通知之后创建的代理对象(mybatis中的mapper对象就是一个代理对象)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d6208aa7a0ea42baa2d0b4c702abf5f4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 3.spring AOP的实现

### 概述

​		对于 AOP 这种编程思想，很多框架都进行了实现。Spring 就是其中之一，可以完成面向切面编程,然而,**AspectJ** 也**实现了 AOP** 的功能，AspectJ 是一个基于 Java 语言的 AOP 框架，它**提供了强大的 AOP 功能**,且其实现方式更为简捷，使用更为方便， 而且还**支持注解式开发**。所以，**Spring 又将 AspectJ 的对于 AOP 的实现也引入到了自己的框架中**。 

​		在 **Spring 中使用 AOP** 开发时，一般**使用 AspectJ 的实现方式**。 AspectJ 是一个优秀面向切面的框架，它扩展了 Java 语言，提供了强大的切面实现。 

**AspectJ** 中**常用的通知**有**五种**类型： 

- 前置通知
- 后置通知
- 环绕通知
- 异常通知
- 最终通知



### 测试AOP功能

#### 1)下载aop相关jar包

```xml
<!--aop-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>
```

#### 2)编写要增强的类和被增强的类

要增强的类UserDao

```java
package com.ffyc.spring.dao;
import org.springframework.stereotype.Repository;

@Repository(value = "userDao")//创建对象
public class UserDao {

    //要增强的方法,在基础上增强一个保存日志的功能
    public void save(){
        System.out.println("保存用户");
    }
}
```

被增强的类

```java
package com.ffyc.spring.aop;

public class AopDemo {
    /**
     * 通知:在连接点上要做的事(增强的功能)
     *      通知分为:前置,后置,异常,环绕通知
     */
    public void savelog(){
        System.out.println("保存日志");
    }
}
```

service层

```java
package com.ffyc.spring.service;

import com.ffyc.spring.dao.UserDao;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

//创建对象
@Service(value = "userService")
public class UserService {

    @Resource(name="userDao")
    private UserDao  userDao;

    public void saveUser() {
        userDao.save();
    }
}
```

#### 3)基于aspectj的xml配置实现AOP

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--把装有增强功能的类交给spring管理-->
    <bean id="aopDemo" class="com.ffyc.spring.aop.AopDemo"></bean>

    <aop:config>
        <!--配置切入点  expression:增强的类的方法-->
        <aop:pointcut id="save" expression="execution(* com.ffyc.spring.dao.UserDao.save(..))"/>

        <!--配置切面-->
        <aop:aspect ref="aopDemo">
            <aop:before method="savelog" pointcut-ref="save"></aop:before>
        </aop:aspect>
    </aop:config>

</beans>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/5291c0d8b2fd4ac3b24b5f8b8ef42628.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


#### 4.在spring配置文件中导入aop的xml

```xml
	<!--注解扫描-->
	<context:component-scan base-package="com.ffyc.spring"></context:component-scan>
    <!--导入实现aop的配置文件-->
    <import resource="aop.xml"></import>
```



测试

```java 
package test;
import com.ffyc.spring.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test2 {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("springConfig.xml");
        UserService service = app.getBean("userService",UserService.class);
                    service.saveUser();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6a4d07c977fd45618239bf20a74ba553.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
### AOP配置实现

所有的**配置**写在一个xml文件中,在spring配置文件中**import**

配置

- 把装有增强功能的类交给spring管理

- <aop:config>

- <aop:pointcut>切入点

- <aop:aspect>切面

  - aop:before
  - aop:after
  - aop:after-returning
  - aop:after-throwing
  - aop:around

  

连接点

```java
	public void save(){
        System.out.println("保存用户1");
    }

    public void save(int a){
        System.out.println("保存用户2");
        int x =10/0;
    }

    public boolean save(String s){
        System.out.println("保存用户3");
        return true;
    }
```



切入点配置

```xml
<!--* :任意返回值类型   (..)任意参数列表-->
<aop:pointcut id="save1" expression="execution(* com.ffyc.spring.dao.UserDao.save(..))"/>
<!--* :任意返回值类型   (int)指定参数列表的方法-->
        <aop:pointcut id="save2" expression="execution(* com.ffyc.spring.dao.UserDao.save(int))"/>
<!--* :指定返回值类型   (String)指定参数列表-->
        <aop:pointcut id="save3" expression="execution(boolean com.ffyc.spring.dao.UserDao.save(String))"/>
```

通知

```java
	public void savelog(){
        System.out.println("保存日志");
    }

    public void ExceptionAdvice(Throwable e){
        System.out.println("*******异常通知********");
        System.out.println(e.getMessage());
    }
    
    public void aroundAdvice(ProceedingJoinPoint point){
        System.out.println("前置通知");
        try {
            point.proceed();
        } catch (Throwable throwable) {
            System.out.println("异常通知");
            throwable.printStackTrace();
        }
        System.out.println("后置通知");
    }
```

配置切面

- before 前置通知
- after     后置通知
- after-returning 最终通知
- after-throwing   异常通知  (throwing =   对应的java方法异常参数)
- around         环绕通知

```xml
 <!--配置切面-->
        <aop:aspect ref="aopDemo">
            <!--<aop:before method="savelog" pointcut-ref="save1"></aop:before>
            <aop:after method="savelog" pointcut-ref="save2"></aop:after>-->
           <!-- <aop:after-returning method="show" pointcut-ref="save3"></aop:after-returning>-->
            <!--<aop:after-throwing method="ExceptionAdvice" pointcut-ref="save1" throwing="e"></aop:after-throwing>-->
            <aop:around method="savelog" pointcut-ref="save1"></aop:around>
        </aop:aspect>
```



### AOP注解实现
- 开启注解扫描
` <context:component-scan base-package="com.ffyc.spring"></context:component-scan>`
- 在spring配置文件中启动AspectJ 实现的AOP
`<aop:aspectj-autoproxy></aop:aspectj-autoproxy>`

```java
package com.ffyc.spring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component//声明此类交由spring管理
@Aspect //声明这是一个切面(把通知添加到切入点)
public class AopDemo {

    /**
     * 通知:在连接点上要做的事(增强的功能)
     *      通知分为:前置,后置,异常,环绕通知
     */
    
    @Before("execution(* com.ffyc.spring.dao.UserDao.save())")//切入点
    public void before(){//通知
        System.out.println("before");
    }

    @After("execution(* com.ffyc.spring.dao.UserDao.save())")
    public void after(){
        System.out.println("after");
    }

    
    @AfterThrowing(value = "execution(* com.ffyc.spring.dao.UserDao.save(int))",throwing = "e")
    public void ExceptionAdvice(Throwable e){
        System.out.println("异常通知");
        System.out.println(e.getMessage());
    }

    /*@Around("execution(* com.ffyc.spring.dao.UserDao.save(int))")
    public void aroundAdvice(ProceedingJoinPoint point){
        System.out.println("前置通知");
        try {
            point.proceed();
        } catch (Throwable throwable) {
            System.out.println("异常通知");
            throwable.printStackTrace();
        }
        System.out.println("后置通知");
    }*/
}
```

**注意**:==环绕==通知可以==替代==**所有通知**,若使用环绕通知,可以不使用其他通知
