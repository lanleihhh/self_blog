
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





# 对AOP的理解？



