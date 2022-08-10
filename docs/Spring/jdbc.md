## spring JDBC

Spring 是个一站式框架：

Spring 自身也提供了**控制层的 SpringMVC** 和 **持久层的 Spring JdbcTemplate**

### spring Jdbc开发步骤

#### 1.下载 Spring JdbcTemplate 的 jar 包,mysql的jar包,阿里druid的jar包

```xml
		<!-- spring-jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.2.RELEASE</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.25</version>
        </dependency>
        
        <!-- 阿里数据源 德鲁伊 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.10</version>
        </dependency>
```



#### 2.导入属性文件(存放jdbc连接的信息)

config.properties文件

```properties
# jdbc驱动
driver=com.mysql.cj.jdbc.Driver
# 连接数据库地址
url=jdbc:mysql://127.0.0.1:3306/mybatis_db?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
# 数据库用户名，用于连接数据库
uname=root
# 用户密码（用于连接数据库）
pwd=root
#连接池初始化时初始化的数据库连接数
initialSize=10
#连接池中最多可空闲maxIdle个连接
minIdle=5
#连接池支持的最大连接数
maxActive=20
```

创建一个存储jdbc连接的配置文件db.xml,导入jdbc属性文件

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


        <!--配置与数据库连接相关配置-->

    <!--spring读入属性文件-->
    <context:property-placeholder location="config.properties"></context:property-placeholder>
</beans>
```

#### 3.管理数据源对象

在db.xml中管理与数据库连接(数据源)的配置

```xml
	<!--配置druid数据库连接对象                           德鲁伊数据源-->
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <!--${属性文件中的属性名}-->
        <property name="username" value="${uname}"></property>
        <property name="password" value="${pwd}"></property>
        <property name="url" value="${url}"></property>
        <property name="driverClassName" value="${driver}"></property>
        <property name="initialSize" value="${initialSize}"></property>
        <property name="minIdle" value="${minIdle}"></property>
        <property name="maxActive" value="${maxActive}"></property>
    </bean>
```



#### 4.在配置文件中创建JdbcTemplate

```xml
	<!--spring提供了jdbcTemplate封装类-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="druidDataSource"></property>
    </bean>
```

配置完JdbcTemplate类后,我们在Java中获取JdbcTemplate的对象,就可以直接使用



**JdbcTemplate 中常用的方法**

- execute() 无返回值，可执行 DDL(create drop alter)，增删改语句
- update() 执行新增、修改、删除语句,返回值为影响的行数
- queryForObject()返回单个对象
- query() 执行查询语句,返回一个List

#### 5.测试JdbcTemplate

UserService类

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

    public void test(){
        userDao.test();
    }
}
```

UserDao类

```java
package com.ffyc.spring.dao;

import com.ffyc.spring.model.Admin;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

/*value=别名 Repository仓库*/
@Repository(value = "userDao")//创建对象
public class UserDao {

    @Autowired(required = true)
    JdbcTemplate jdbcTemplate;

    public void test(){
        //execute执行DDL语句(create drop alter)
        //jdbcTemplate.execute("create table test(id int,name varchar(10))");

 		//update 修改,删除,新增
 		//jdbcTemplate.update("insert into admin(account,pass_word,sex)values(?,?,?)","admin888","666666","男");

        
       /* //查询关系中行数
       int count = jdbcTemplate.queryForObject("select count(*) from admin",Integer.class);
        System.out.println(count);//
        */

        //查询绑定变量
       /* String account = jdbcTemplate.queryForObject("select account from admin where id=?", String.class,"1");
        System.out.println(account);*/

        //查找单个对象
        /*
        Admin admin = jdbcTemplate.queryForObject("select * from admin where id=?",
                new Object[]{1},//id=1
                new RowMapper<Admin>(){
                    @Override
                    public Admin mapRow(ResultSet resultSet, int i) throws SQLException {
                        Admin admin1 = new Admin();
                        admin1.setId(resultSet.getInt("id"));
                        admin1.setAccount(resultSet.getString("account"));
                        admin1.setPassWord(resultSet.getString("pass_word"));
                        admin1.setSex(resultSet.getString("sex"));
                        return admin1;
                    }
                });
        System.out.println(admin);*/



        //查找多个对象
        List<Admin> list = jdbcTemplate.query("select * from admin",
                new RowMapper<Admin>() {
                    @Override
                    public Admin mapRow(ResultSet resultSet, int i) throws SQLException {
                        Admin admin1 = new Admin();
                        admin1.setId(resultSet.getInt("id"));
                        admin1.setAccount(resultSet.getString("account"));
                        admin1.setPassWord(resultSet.getString("pass_word"));
                        admin1.setSex(resultSet.getString("sex"));
                        return admin1;
                    }
                });
        System.out.println(list);
    }
}
```

查找指定的一个对象

![在这里插入图片描述](https://img-blog.csdnimg.cn/bc873fff52ce4dc3b43d5806aec4600b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


查找多个对象的集合

![在这里插入图片描述](https://img-blog.csdnimg.cn/b263622abaf8414891259cb4563b976e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


