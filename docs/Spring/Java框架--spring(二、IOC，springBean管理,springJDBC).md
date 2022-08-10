# Java框架--spring(二、IOC，springBean管理)

## 一、IOC控制(权)反转

​		**IOC**(Inverse of Control),反转控制,是一种设计思想,原来需要在程序中手动地创建对象(在哪需要在哪new),现在我们**把创建对象的控制权给spring框架管理**

- 正控:需要对象时,自己**手动创建**
- 反控:需要对象时,**从spring容器中获取**需要的对象

​        IOC 容器是具有**依赖注入**功能的容器，**负责**对象的**实例化**、对象的**初始化**，对象和对象之间**依赖关系配置**、对象的**销毁**、对外提供对象的**查找**等操作，对象的整个生命周期都是由容器来控制。我们需要使用的对象都由 ioc 容器进行管理，不需要我们再去手动通过 new 的方式去创建对象，由 ioc 容器直接帮我们组装好，当我们**需要使用的时候直接从 ioc 容器中直接获取**就可以了。

反转控制权的**目的**:降低耦合度

底层实现:**解析xml** / **扫描注解标签** + **工厂模式** + **反射机制**

## 二、spring Bean管理

### 基于xml配置方式

bean 配置需要 spring 管理的类

```xml
<bean id="user" name="user1,user2" scope="prototype" class="com.ffyc.spring.model.User"> </bean>
```

- **id**:生成的对象标识
- **class**:类的地址(全类名)
- **name**:对象别名(可以有多个)
- **scope**:作用域/范围  scope默认是singleton    
  - singleton:单例模式,整个应用程序只创建一次,多次获取同一个对象(在spring容器(ClassPathXmlApplicationContext)启动时就创建)    
  - prototype:原型(多例),每次获取时会创建一个新对象

- **request**:每次 Http 请求都会创建一个 Bean, 仅用于 WebApplicationContext环境

- **session**:同一个 HttpSession 共享一个 Bean, 不同 Session 使用不同的 Bean, 用于 WebApplicationContext环境

#### xml配置方式依赖注入(DI)

- **DI**：Dependency Injection 依赖注入,在spring创建对象时,**将**对象的**属性**(基本类型,引用类型,集合)通过配置**设置给该对象.**实现IOC(控制反转)需要DI(依赖注入)支持

**注入方式**:

- setter方法注入

  ```xml
  <property name="name" value="张三"></property>
  <property name="age" value="123"></property>
  ```

- 构造方法注入

  - 通过类型注入byType

    ```xml
    <constructor-arg type="java.lang.String" value="张三"></constructor-arg>
    ```

  - 通过属性名注入byName

    ```xml
    <constructor-arg name="name" value="张三"></constructor-arg>
    ```

示例:setter注入

给User类加一个集合属性

```java
private List<Integer> list;

public void setList(List<Integer> list) {
	this.list = list;
}
```

spring配置文件中注入属性值

```xml
<!--setter-->
<bean id="user" class="com.ffyc.spring.model.User">
    <property name="name" value="张三"></property>
    <property name="age" value="123"></property>
    <property name="list">
        <list>
            <value>1</value>
            <value>2</value>
            <value>3</value>
        </list>
    </property>
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
        ApplicationContext app = new ClassPathXmlApplicationContext("springConfig.xml");
        User user =  (User) app.getBean("user",User.class);
        System.out.println(user);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c924d94bfc394576a8818d25da494567.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


可以看到set方法被调用了,**通过set注入,会自动调用实体类的setter方法为属性注入值**



### 基于注解方式

使用注解方式的条件:

1. 使用注解功能需要**依赖**一个spring **aop jar包**,注解功能封装在AOP包中

![在这里插入图片描述](https://img-blog.csdnimg.cn/945748f16f8b49e8ac8b37199dab37e1.png#pic_center)


在导入spring核心包springframework时,自动依赖了aop的jar包

2. 开启注解扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xmlns:context="http://www.springframework.org/schema/context"       xmlns:aop="http://www.springframework.org/schema/aop"       xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd        http://www.springframework.org/schema/context        http://www.springframework.org/schema/context/spring-context.xsd        http://www.springframework.org/schema/aop  <!--注解需要aop-->     http://www.springframework.org/schema/aop/spring-aop.xsd">
       
   
       <!--开启spring注解扫描    base-package:扫描指定包下所有类是否有注解标签        --> 
       <context:component-scan base-package="com.ffyc.spring"></context:component-scan>
   
   </beans>
   ```

#### 注解方式创建对象

- 注解方式**创建对象**(写在类上方)

  - **@Component**(value = "user") 在model里使用,不写vlaue时,默认当前类名

    相当于配置文件中的`<bean id="user" class="com.ffyc.spring.model.User"></bean>`

 	```java
    @Component(value = "user") 
    public class User {}
    
    /*//不写value
    @Component
    public class User {}*/
    ```

  - **@Service**(value = "userService")在业务逻辑层中使用(**service层**)

    ```java
    //创建对象
    @Service(value = "userService")
    public class UserService {}
    ```

  - **@Repository**(value = "userDao")在数据访问层(持久层)使用(**dao层**)

    ```java
    @Repository(value = "userDao")//创建对象
    public class UserDao {}
    ```

    

- 注解方式**指定生成对象单例/多例**

  - @**Scope**(value = "singleton")指定生成对象的策略(**singleton**-单例 **prototype**-原型(多例))

    ```java
    @Component(value = "user") 
    @Scope(value = "singleton")
    public class User {}
    ```

    

#### 注解方式注入属性

注解方式**注入属性**(写在属性上方)

- **@Autowired** 自动注入

  - 属性required:设置属性取值是否允许null值  required=true/false  默认**true**

  - 可以写在**属性上**或**setter方法上**,写在属性上可以不写setter方法

  - 注入方式:

    - by**Type**,按属性类型注入,默认使用byType方式

      ```java
      	@Autowire
          private UserDao userDao;
          //spring提供的注入方式,Autowired(required = true)默认true 注入的值不能为Null
      ```

    - by**Name**,按属性名称注入,若想按名称注入,可以结合**@Qualifier**注解一起使用

- **@Qualifier** 自动注入

  - value=被注解属性的id值(属性为User id=user)

  - ```java
    	@Autowired//写在属性上可以不写setter方法
        @Qualifier(value = "userDao")
        private UserDao userDao;
    ```

- **JDK 注解@Resource** 自动注入

  - Spring 提供了对 **jdk** 中**@Resource** 注解的支持。
    - **@Resource** 注解既可以**按名称**匹配 Bean，也可以**按类型**匹配 Bean。**默认按照 ByName** 自动注入
    - 属性name=被注解属性的id值(属性为User id=user)

  - ```java
    @Resource(name="userDao")
    private UserDao  userDao;
    ```

### 注解方式与XML方式对比

|             | 优点                                                         | 缺点                                                         |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 注解方式    | 方便,直观,高效（**代码少**，没有配置文件的书写那么复杂）     | 以硬编码的方式写入到 Java 代码中，**修改是需要重新编译代码的** |
| xml配置方式 | **配置和代码是分离**的,在 xml 中做**修改**，无需编译代码，**只需重启服务器**即可将新的配置加载 | 编写**麻烦，效率低**，大型项目过于复杂                       |

