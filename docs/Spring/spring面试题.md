
![在这里插入图片描述](https://img-blog.csdnimg.cn/f3b1d30a100d4b50bb98c606f3752280.png)

# BeanFactory 和 ApplicationContext

![image-20220610081405161](https://img-blog.csdnimg.cn/img_convert/e799335aacf127f65e7bad6ed11616d7.png)

![image-20220610082317561](https://img-blog.csdnimg.cn/img_convert/2bd785d48d4ec008ad88a13e22b8a5fe.png)

**相同**

- 都可以使用getBean()获取bean对象

- 都是接口，都是IOC容器
- 都可以用来配置xml属性，支持属性的自动注入

**BeanFactory**

- BeanFactory是spring中最基础的接口(容器)，定义了管理bean的最基础的方法

- 是spring中的原始Factory，不支持扩展AOP，Web等spring插件

- 调用getBean()获取bean对象时，才开始实例化（延迟初始化)

**ApplicationContext**

- 继承了BeanFactory，具有它的所有功能，支持AOP、Web等插件。
- 在容器启动时就实例化了所有单例bean对象

# Spring 中 bean的生命周期

5个大步骤

1. **实例化  Instantiation**
2. **属性赋值  Populate**
3. **初始化  Initialization**
4. **使用 bean**
5. **销毁  Destruction**

细化步骤

1. 对象实例化
2. 属性赋值
3. 初始化操作
   1. 若Bean实现了`BeanNameAware`接口，调用`setBeanName()`，将bean的id传给setBeanName
   2. 若Bean实现了`BeanFactoryAware`接口,调用`setApplicationContext()`进行初始化
   3. 若Bean实现了`ApplicationContextAware`接口，调用`setApplicationContext()`进行初始化
   4. 若Bean实现了`BeanPostProcessor`接口（AOP），调用`postProcessBeforeInitialization()`进行初始化
   5. 若Bean实现了`InitializingBean`接口 或 bean使用`<bean init-method="初始化方法">`声明了初始化方法，调用afterPropertiesSet()进行初始化
   6. 若Bean实现了`BeanPostProcessor`接口（AOP），调用`postProcessAfterInitialization()`进行初始化

4. Bean创建完成放到容器中，可以使用，直到应用程序上下文被销毁
5. 如果Bean实现了`DisposableBean`接口，或 配置了 <bean destroy-method="销毁方法"> ，会调用destory()方法销毁Bean对象
![在这里插入图片描述](https://img-blog.csdnimg.cn/73d927146f344ca4bc3d232e788979cf.png)

# Spring 中 Bean 是否线程安全？
![在这里插入图片描述](https://img-blog.csdnimg.cn/46511720146646a398bc160dac82d00d.png)
==不一定：有状态单例Bean(存储数据的单例bean)才会存在线程安全问题==
## Bean的scope作用域
- **singleton** 单例
  第一次请求被创建，所有线程共享一个实例，存在线程安全问题，是spring默认作用域
- **prototype** 原型
  每次请求都会创建一个新对象，不是线程共享，不存在线程安全问题
 - request 请求
	为每个HTTP请求创建一个新的Request-Processor对象，当请求结束后，该对象实生命周期就结束
- session 会话：同一个会话共享一个实例
- global session 全局会话：所有会话共享一个实例

## 有状态Bean和无状态Bean
- 有状态Bean: **存储数据**的Bean对象
	如User,Admin的对象需要存数据，就是有状态的Bean
- 无状态Bean：**不存储数据**，只是用它来调用一些方法
	eg:Controller、Service、Dao对象都是无状态的bean，并不保存数据

有状态的单例Bean会存在线程安全问题
@Controller
@Service
@Repository
这些容器中默认是单例，如果只是调用方法，是线程安全的
## 如何保证 Bean 线程安全？
1. 将scope设置为**prototype**，原型bean会在每个线程中都创建一个新的对象
2. 使用**ThreadLocal**，为每个线程提供一份变量副本，不必去竞争同一份资源

# Spring中两个id相同的Bean会报错吗？

会报错

id是Spring中用来区分Bean对象的唯一标志符号，在Spring启动后会验证id的唯一性

1. 在同一个XML配置文件里面，不能存在id相同的两个bean，否则spring容器启动的时候会报错。

   报错发生在Spring对xml文件进行解析**转化为BeanDefinition**的这个阶段

2. 两个不同的xml配置文件中可以存在id相同的Bean，一个xml文件通过import进行引用另一个，IOC容器在加载Bean对象的时候默认会把多个相同id的Bean进行覆盖

3. 在Spring3.x以后，提供了@Configuration注解去声明一个配置类，使用@Bean注解实现Bean的声明，这种声明方式取代了xml中配置Bean对象的方式

   如果在同一个配置类中声明多个相同name的Bean，Spring只会注册第一个Bean的实例，后面相同name的Bean不会再注册了

   如果使用@Autowired注解根据类型实现依赖注入，因为IOC容器只有A1的实例，所以启动的时候会提示找不到A2这个实例。

   如果使用@Resource注解根据名词实现依赖注入，在IOC容器里面得到的实例对象是A1，

   于是Spring把A1这个实例赋值给A2，就会提示类型不匹配错误。

   这个错误，是在Spring IOC容器里面的Bean初始化之后的**依赖注入阶段发生**的。





# 循环依赖

在Java中，循环依赖是正常的，如员工关联了一个部门对象，一个部门关联了多个员工对象，在使用的时候没有问题。

但在Spring中，对象的创建需要注入依赖对象的值才可以，A对象创建需要先注入B，创建B又要先注入A，循环依赖就像一个死循环一样
![在这里插入图片描述](https://img-blog.csdnimg.cn/3e3285d305f24d61a1e57aa1ef6fcfff.png)

==循环依赖的原因是因为Bean的生命周期==
在Java中初始化时，可以允许依赖的属性为null
Spring中不允许，需要为对象注入值
## 解决循环依赖
Spring的**二级缓存**可以解决循环依赖的问题
![在这里插入图片描述](https://img-blog.csdnimg.cn/cc5a529ad4454f4c9a97470d98d5a5b7.png)


Spring的缓存
- 一级缓存 singletonObjects：保存实例化、属性注入、**初始化完成的Bean**（经历了完整的生命周期，是一个成品）
- 二级缓存 earlySingletonObjects：**保存实例化**完成，还没有注入属性的Bean(实例化完成，对象不为null，就可以注入，解决了循环依赖)
- 三级缓存 singletonFactories：保存Bean工厂(本质是一段lambda表达式)，以便后期**添加其他代理对象**(如事务管理中代理对象的生成)

==构造方法注入无法解决循环依赖问题==
在构造方法中使用@Lazy标识依赖的属性参数，可以解决

# 对IOC的理解？

IoC（Inverse of Control:控制反转）是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由Spring框架来管理。IOC思想是基于IOC容器来完成的，IOC容器底层就是对象工厂（BeanFactory接口）。IOC的原理是基于xml解析、工厂设计模式、反射来实现的。 IoC 容器实际上就是个Map（key，value）Map 中存放的是各种对象。

通俗易懂的一句话结论：之前需要我们自己手动new对象的，但是我们现在不需要反复去new对象了，而是把new对象的主动权交给IOC容器，我们什么时候用什么时候取就可以了。

随着技术越来越先进，在Spring原生中我们觉得xml文件中配置还是有点麻烦，后来就有了springboot内部集成了这些功能，我们直接一个注解就可以了，方便了很多。




# 对AOP的理解？

AOP(面向切面编程)

AOP代表的是一个横向的关系，剖开对象的内部，并且把影响多个类的共同行为抽取出来，作为公共模块（叫做切面Aspect），然后再通过织入的方式把这个切面放进去。理解来说：就是能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

通俗易懂的一句话结论：就是不通过修改源代码方式，在主干功能里面添加新功能。

AOP底层是通过动态代理来实现的，同时有JDK动态代理和CGLIB动态代理两种方式：

1、如果目标对象实现了接口，默认情况下会采用JDK的动态代理(反射)，即创建接口实现类代理对象，增强类的方法

2、如果目标对象没有实现接口，必须采用CGLIB库(字节码技术)，即创建子类的代理对象，spring会自动在JDK动态代理和CGLIB之间转换



如果目标对象实现了接口，也可以强制使用CGLIB

```xml
1.添加CGLIB库(aspectjrt-xxx.jar、aspectjweaver-xxx.jar、cglib-nodep-xxx.jar)
2.在Spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class=“true”/>
```

# JDK和CGLIB动态代理的区别

JDK代理使用的是反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
CGLIB代理使用字节码处理框架ASM，对代理对象类的class文件加载进来，通过修改字节码生成子类。
JDK创建代理对象效率较高，执行效率较低；
CGLIB创建代理对象效率较低，执行效率高。
JDK动态代理机制是委托机制，只能对实现接口的类生成代理，通过反射动态实现接口类；
CGLIB则使用的继承机制，针对类实现代理，被代理类和代理类是继承关系，所以代理类是可以赋值给被代理类的，因为是继承机制，不能代理final修饰的类。
JDK代理是不需要依赖第三方的库，只要JDK环境就可以进行代理，需要满足以下要求：
 1.实现InvocationHandler接口，重写invoke()
 2.使用Proxy.newProxyInstance()产生代理对象
 3.被代理的对象必须要实现接口

CGLib 必须依赖于CGLib的类库,需要满足以下要求：
 1.实现MethodInterceptor接口，重写intercept()
 2.使用Enhancer对象.create()产生代理对象

# Spring的事务传播机制

- **REQUIRED**(需要传播)：默认的传播机制，支持当前事务，如果没有事务会创建一个新的事务
- **REQUIRES_NEW**(需要新的)：创建一个新的事务并挂起当前事务
- **SUPPORTS**(支持传播)：支持当前事务，如果没有事务的话以非事务方式执行
- NOT_SUPPORTED(不支持传播)：以非事务方式执行，如果当前存在事务则将当前事务挂起
- MANDATORY(强制传播)：支持当前事务，如果没有事务抛出异常
- NEVER(绝不传播)：以非事务方式进行，如果存在事务则抛出异常
- NESTED(嵌套传播)：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则进行与REQUIRED类似的操作



# REQUIRED 和 NESTED 区别

REQUIRED ： 如果存在事务，就在当前事务里运行，否则的话就创建一个新的事务

- 事务传播机制为REQUIRED时，子事务会与父事务一起提交或者一起失败

NESTED：如果当前事务存在，就在嵌套事务中执行；没有事务就新建一个事务

- 事务传播机制为NESTED时

  如果嵌套事务发生异常，嵌套事务进行回滚，不会影响父事务

  如果父事务发生异常，嵌套事务会跟着父事务回滚







# Spring事务是如何运行的？

Spring中有两种实现事务的方式

编程式事务：TransactionTempalate调用commit、rollback

声明式事务：

- 基于XML声明：配置 spring 事务管理类，指定事务传播机制，指定事务的作用方法
- 基于@Transaction声明：开启注解事务管理，在service层使用@Transaction注解



事务运行：



Spring事务使用AOP的机制实现，会在@Transactional注解修饰的方法前后分别织入开启事务的逻辑，以及提交或回滚的逻辑。@Transactional可以修饰在方法或者类上，区别就在于修饰于类上的，会对该类下符合条件的方法（例如private修饰的方法就不符合条件）前后都织入事务的逻辑。

具体的处理逻辑如下（具体的方法路径为TransactionInterceptor.invoke -> TransactionAspectSupport.invokeWithinTransaction）：

1.1 开启事务(DataSourceTransactionManager.doBegin)


这里主要做了获取连接，并关闭自动提交，将@Transactional注解中的一些参数初始化到txObject对象中。

1.2 异常回滚（TransactionAspectSupport.completeTransactionAfterThrowing）

 这里是事务异常回滚的地方，这里有个注意点是回滚会先用rollbackOn这个方法判断，默认情况下只有RunTimeException以及Error是会进行回滚的，除非在@Transactional显式声明了rollbackFor。


# Spring事务是怎么实现的？

基于**数据库事务和AOP机制**实现: 会在@Transactional注解修饰的方法前后分别织入开启事务的逻辑，以及提交或回滚的逻辑

1. 首先对于使用了@Transactional注解的Bean，Spring会创建一个代理对象作为Bean
2. 当调用代理对象的方法时，会先判断该方法上是否加了@Transactional注解
3. 如果加了，那么则利用事务管理器创建一个数据库连接
4. 并且修改数据库连接的autocommit属性为false，禁止此连接的自动提交，这是实现Spring事务非常重要的一步
5. 然后执行当前方法，方法中会执行sql
6. 执行完当前方法后，如果没有出现异常就直接提交事务
7. 如果出现了异常，并且这个异常是需要回滚的就会回滚事务，否则仍然提交事务
   

> Spring事务的隔离级别对应的就是数据库的隔离级别
>
> Spring事务的传播机制是Spring事务自己实现的，也是Spring事务中最复杂的
>
> Spring事务的传播机制是基于数据库连接来做的，一个数据库连接就是一个事务;
>
> 如果传播机制配置为需要新开一个事务，那么实际上就是先新建一个数据库连接，在此新数据库连接上执行sql

# SpringBoot自动装配

1.springboot自动装配主要是基于注解编程，和预定优于配置的思想来进行设计的

 

自动装配就是自动地把其他组件中的Bean装载到IOC容器中，不需要开发人员再去配置文件中添加大量的配置，

我们只需要在springboot的启动类上添加一个SptingBootApplication的一个注解，这样就可以开启自动装配

这种自动装配的思想在spring3.x以后就支持了，我们只需要在类上添加一个叫做@Enable的注解就可以了，只是spring没有向SpringBoot这样全面去设计，

因此Spring和SpringBoot最大的区别就是在于SpringBoot的自动装配

 

2.自动装配的原理又是什么？

@SptingBootApplication这个注解是暴露给用户使用的一个入口，它的底层其实是由@EnableAutoConfiguration这个注解来实现的，

自动装配的实现，归纳为以下三个核心的步骤：

1. 启动依赖组件的时候

   组件中必须要包含@Configuration的配置类，在这个配置类里面声明为Bean注解，然后将方法的返回值或者是属性注入到IOC容器中

2. 第三方jar包，SpringBoot会采用SPI机制，在/META-INF/目录下增加spring.factories文件，然后SpringBoot会自动根据约定，自动使用SpringFactoriesLoader来加载配置文件中的内容

3. Spring获取到第三方jar中的配置以后会调用ImportSelector接口来完成动态加载，

   这样设计的好处，在于大幅度减少了臃肿的配置文件，而各模块之间的依赖，也深度的解耦，

   比如我们使用Spring创建Web程序的时候需要引用非常多的Maven依赖，而SpringBoot中只需要引用一个Maven依赖就可以来创建Web程序

   并且SpringBoot把我们常用的依赖都放在了一起，，我们只需要去引入spring-boot-starter-web这个依赖就可以去完成一个简单的Web应用

   以前我们使用Spring的时候需要xml文件来配置开启一些功能，现在使用SpringBoot就不需要xml文件了，

   只需要一个加了@Configuration注解的类，或者是实现了对因接口的配置类就可以了

   SpringBoot自动装配是Spring的完善和扩展，就是为了我们便捷开发，方便测试和部署，提高效率而诞生的框架技术。



