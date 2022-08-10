# spring整合mybatis
集成mybaits的原理就是将SqlSessionFactory交给spring来管理，由spring管理对dao接口的**代理实现**

## 1. 导入 jar 包
mybatis

```xml
<!--mybatis-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.2</version>
</dependency>
```

mybatis-spring整合包
```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.1</version>
</dependency>
```
mysql-connector-java 数据库驱动
```xml
<!-- mysql-connector-java -->
<dependency>
     <groupId>mysql</groupId>
     <artifactId>mysql-connector-java</artifactId>
     <version>8.0.16</version>
</dependency>
```
spring相关jar包

```xml
<!--spring.context-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>

<!-- spring-jdbc-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>

<!-- spring-aop -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>

<!-- 阿里数据源 -->
<dependency>
     <groupId>com.alibaba</groupId>
     <artifactId>druid</artifactId>
     <version>1.1.10</version>
</dependency>
```

## 2. 配置spirng.xml文件

```xml
<!--开启spring注解扫描
base-package:指定包下所有类是否有注解标签-->
<context:component-scan base-package="com.ffyc.ssm"></context:component-scan>

<import resource="db.xml"></import>
<import resource="spring_mybatis.xml"></import>
<import resource="spring_mvc.xml"></import>
```

## 3. 配置spring_mybaits.xml
**配置 sqlSessionFactory，指定生成接口代理**

```xml
<!--1.spring管理生成SqlSessionFactory,读取mybatis的各项配置-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="druidDataSource"></property>
    <property name="configLocation" value="classpath:mybatis.xml"></property>
    <property name="mapperLocations" value="classpath:mapper/*.xml"></property>
</bean>

<!--2.生成指定包下面的接口代理对象-->
<bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.xx.xx.dao"></property>
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
</bean>
```
## 4. 配置mybaits.xml
```xml
<!--配置-->
<configuration>

    <settings>
        <!-- 配置日志实现, 使用log4j,添加log4j -->
        <setting name="logImpl" value="LOG4J"/>
        <!--开启驼峰映射  设置启用数据库字段下划线映射到java对象的驼峰式命名属性，默认为false-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!-- 开启懒加载
        <setting name="lazyLoadingEnabled" value="true"/>-->
        <!--lazyLoadTriggerMethods:指定哪些方法触发延迟加载-->
        <setting name="lazyLoadTriggerMethods" value=""/>
        <!--开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>

    <typeAliases>
        <package name="com.xx.xx.model"/>
    </typeAliases>


</configuration>
```
## 5.配置数据库连接db.xml

```xml
<!--配置与数据库连接相关配置-->

    <!--1.spring读入属性文件-->
    <context:property-placeholder location="classpath:config.properties"></context:property-placeholder>

    <!--2.配置druid数据库连接对象-->
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${uname}"></property>
        <property name="password" value="${pwd}"></property>
        <property name="url" value="${url}"></property>
        <property name="driverClassName" value="${driver}"></property>
        <property name="initialSize" value="${initialSize}"></property>
        <property name="maxActive" value="${maxActive}"></property>
    </bean>

    <!-- 3.声明式事务 配置spring事务管理类-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="druidDataSource"></property>
    </bean>

    <!--4.开启注解事务管理-->
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```


## 6. 配置config.properties文件

```java
# jdbc驱动
driver=com.mysql.cj.jdbc.Driver
# 连接数据库地址
url=jdbc:mysql://127.0.0.1:3306/ssm?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
# 数据库用户名，用于连接数据库
uname=root
# 用户密码（用于连接数据库）
pwd=lanlei6843
#连接池初始化时初始化的数据库连接数
initialSize=10
#连接池支持的最大连接数
maxActive=20
```

## 7. 配置日志文件log4j.properties

```xml
log4j.rootLogger=debug,stdout,D
#System out Console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=[%p] %d{yyyy-MM-dd HH:mm:ss,SSS} %m%n
#System out File
log4j.appender.D=org.apache.log4j.FileAppender
log4j.appender.D=org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File=E://logs/log.log
log4j.appender.D.Append=true
log4j.appender.D.layout=org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ] -[%l] %m%n
```
