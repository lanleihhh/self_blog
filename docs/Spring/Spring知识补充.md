
![在这里插入图片描述](https://img-blog.csdnimg.cn/f3b1d30a100d4b50bb98c606f3752280.png)

# 1.BeanFactory 和 ApplicationContext

![image-20220610081405161](https://img-blog.csdnimg.cn/img_convert/e799335aacf127f65e7bad6ed11616d7.png)

![image-20220610082317561](https://img-blog.csdnimg.cn/img_convert/2bd785d48d4ec008ad88a13e22b8a5fe.png)

**BeanFactory**

- BeanFactory是spring中最基础的接口(容器)，定义了管理bean的最基础的方法

![image-20220610083628605](https://img-blog.csdnimg.cn/img_convert/8999530ae66a2565792cde4b57bd112c.png)

- 是spring中的原始Factory，不支持扩展AOP，Web等spring插件

- 需要获取bean对象时，才开始创建(延迟初始化)

**ApplicationContext**

- 继承了BeanFactory，具有它的所有功能，支持AOP、Web等插件。
- 在启动spring时就创建所有bean对象

# 2.Spring 中 bean的生命周期

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

# 3.Spring 中 Bean 是否线程安全？
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


# 4.循环依赖
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
- 一级缓存 singletonObjects：保存实例化、属性注入、**初始化完成的Bean**
- 二级缓存 earlySingletonObjects：**保存实例化**完成的Bean(实例化完成，对象不为null，就可以注入，解决了循环依赖)
- 三级缓存 singletonFactories：保存Bean工厂，以便后期**添加其他代理对象**(如事务管理中代理对象的生成)

==构造方法注入无法解决循环依赖问题==
在构造方法中使用@Lazy标识依赖的属性参数，可以解决
# 5.过滤器与拦截器
|          |                            过滤器  Filter                          | 拦截器    Interceptor                              |
| :------: | :----------------------------------------------------------: | :-------------------------------------- |
| 实现方式 |                         基于函数回调                         | 基于Java反射(动态代理)                  |
| 使用范围 | ![在这里插入图片描述](https://img-blog.csdnimg.cn/8f092972c2564693b4eb60032e11cfe8.png)servlet是tomcat等服务器实现的，需要依赖Tomcat等容器 |是spring组件； Java程序中都可以使用![在这里插入图片描述](https://img-blog.csdnimg.cn/4ac27b412f1d404b8659574b424b221c.png)  |
| 触发时机 |                     到达servlet之前触发                      | 进入servlet之后，到达Controller之前触发 |
| 请求范围 |               可拦截所有进入容器(Tomcat)的请求               | 只能拦截进入控制器(`DispatcherServlet`)的请求                |

![在这里插入图片描述](https://img-blog.csdnimg.cn/0ea0222cf0a94ac3a548a6988cf7f51d.png)

## 过滤器
serlvet 过滤器
![在这里插入图片描述](https://img-blog.csdnimg.cn/0df18eee7e9149fc99a78d1cbc206cfb.png)

```java
//登录验证过滤器
public class IsLoginFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //ServletRequest向下转型为,HttpServletRequest
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        //获取session对象
        HttpSession session = request.getSession();
        session.setMaxInactiveInterval(10 * 60);//设置10分钟 后 session对象销毁
        //从session对象中拿到User对象
        SystemManager sm = (SystemManager) session.getAttribute("sm");
        if (sm == null) {// sm为null 代表服务器session对象已经销毁
            PrintWriter out = servletResponse.getWriter();
            out.println(222);//222 : 用户对象不存在,提示重新登录
        } else {//没有销毁就继续走下一个doFilter
            filterChain.doFilter(servletRequest, servletResponse);
        }
    }
}

/**
 * 通一编码过滤器
 */
class EncodFilter implements Filter {

    String str;

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        str = filterConfig.getInitParameter("code");

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //设置编码格式
        servletRequest.setCharacterEncoding(str);
        //让请求继续
        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```

### 使用过滤器的方法
1、在web.xml中配置

```xml
<!--过滤器-->
<filter>
    <filter-name>encod</filter-name>
    <filter-class>com.company.dormms.filter.EncodFilter</filter-class>
    <init-param>
        <param-name>code</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>encod</filter-name>
    <url-pattern>/back/*</url-pattern>
</filter-mapping>

<filter>
    <filter-name>isLogin</filter-name>
    <filter-class>com.company.dormms.filter.IsLoginFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>isLogin</filter-name>
    <url-pattern>/back/build</url-pattern>
    <url-pattern>/back/buildManager</url-pattern>
</filter-mapping>
```

## 拦截器
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca191022eedf47f191967925eaafa719.png)

```java
public class LoginInterceptor implements HandlerInterceptor {
    //* 当请求到达控制器之前被执行 true--继续向下执行,到达下一个拦截器,或控制器 false--不会继续向下执行*//*
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession httpSession=request.getSession();
        Manage manage = (Manage) httpSession.getAttribute("manage");
        if(manage !=null){
            return  true;
        }else{
            response.getWriter().println(203);
            return  false;
        }
    }
    //*控制器方法执行之后执行*//*
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }
    //*整个请求结束后执行*//*
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```
使用：

```xml
<mvc:interceptors>
   <mvc:interceptor>
       <!--哪些请求进入拦截器-->
       <mvc:mapping path="/**"/>
       <!--哪些请求不进入拦截器-->
       <mvc:exclude-mapping path="/login/login"/>
       <mvc:exclude-mapping path="/**/*.html"/>
       <mvc:exclude-mapping path="/**/*.css"/>
       <mvc:exclude-mapping path="/img/**"/>
       <mvc:exclude-mapping path="/**/*.js"/>
       <bean id="loginId" class="com.ffyc.team.util.LoginInterceptor"></bean>
   </mvc:interceptor>
</mvc:interceptors>
```

