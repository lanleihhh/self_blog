## 动态sql

​		我们以前使用传统的JDBC写复杂一点的sql语句时,往往需要**拼接字符串**,容易因为标点符号错误或缺失等导致整条sql是不能运行的.Mybatis提供了**动态sql**来解决以前令人头疼的事.

​		动态sql可以根据用户提供的参数,动态地决定查询语句的条件或sql语句的内容

Mybatis中用于实现动态sql的元素:

- **if**
- **where**
- **trim**
- **set**
- **choose(when,otherwise)**
- **foreach**

### if元素

if标签可以对传入的条件进行判断

```xml
<select id="findEmployee" resultMap="employeeMap">
        SELECT
            e.id,
            e.name ename,
            e.age,
            e.sex,
            d.name dname,
            a.account
            FROM  employee e
        LEFT JOIN dept d ON e.dept_id=d.id
                <!--                where 1=1 不管name和age是否为空,总会正常执行-->
        LEFT JOIN admin a ON e.admin_id=a.id where 1=1
            <if test="name!=null &amp; name !=''">
                and e.name=#{name}
            </if>
            <if test="age!=null &amp; age !='' ">
                and e.age=#{age}
            </if>
    </select>
```



### where

当查询条件的个数不确定的情况下,使用`where`元素.

可以根据里面`if`是否有成立的,动态添加`where`关键字,会自动删除`and` `or` 等开头的关键字

```xml
<select id="findEmployee" resultMap="employeeMap">
        SELECT
            e.id,
            e.name ename,
            e.age,
            e.sex,
            d.name dname,
            a.account
            FROM  employee e
        LEFT JOIN dept d ON e.dept_id=d.id
        LEFT JOIN admin a ON e.admin_id=a.id
        <where>
            <if test="name!=null &amp; name !=''">
                e.name  =#{name}
            </if>
            <if test="age!=null &amp; age !='' ">
                and e.age  =#{age}
            </if>
        </where>
    </select>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/3ee8822c15874c0d84c97ee7b43fac2f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/9f3755b5d9d64d6a8584ef0399dd548d.png#pic_center)




### set

set元素可以**去掉**set语句中的**最后一个逗号**

```xml
	<update id="updateEmp" parameterType="Employee">
        update employee
        <set>
            <if test="name!=null">
                name =#{name},
            </if>
            <if test="sex!=null">
                sex =#{sex},
            </if>

            <if test="dept.id!=null">
                dept_id=#{dept.id},
            </if>
        </set>
        where id=1
    </update>
```

```java
	@Test
    public void update(){
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        Employee employee = new Employee();
                 employee.setName("王玄策");
                 employee.setSex("男");
        Dept dept = new Dept();
             dept.setId(2);
             employee.setDept(dept);
             mapper.updateEmp(employee);
        sqlSession.commit();
        sqlSession.close();
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/809dc6c000294c44a4667d1fe28285ff.png#pic_center)






### trim

使用trim元素也可以实现where 和 set 元素的功能

trim实现where

```xml
	<!--前缀为where-->
	<select id="findEmployee" resultMap="employeeMap">
        SELECT
        e.id,
        e.name ename,
        e.age,
        e.sex,
        d.name dname,
        a.account
        FROM  employee e
        LEFT JOIN dept d ON e.dept_id=d.id
        LEFT JOIN admin a ON e.admin_id=a.id
        <trim prefix="where" prefixOverrides="and|or">
            <if test="name!=null &amp; name !=''">
                and  e.name  =#{name}
            </if>
            <if test="age!=null &amp; age !='' ">
                and e.age  =#{age}
            </if>
        </trim>
    </select>
```



实现set

```xml
	<!--前缀为set-->
	<update id="updateEmp" parameterType="Employee">
        update employee
        <trim prefix="set" suffix="where" suffixOverrides=",">
            <if test="name!=null">
                name =#{name},
            </if>
            <if test="sex!=null">
                sex =#{sex},
            </if>

            <if test="dept.id!=null">
                dept_id=#{dept.id},
            </if>
        </trim>
        id=1
    </update>
```



### choose(when,otherwise)

多路选择,和Java中的switch类似

when相当于switch中的case,当`<when test="条件 ">`条件成立,执行when中的语句

otherwise则类似default,当都不成立时,执行它

```xml
	<!--  &amp;是&的转义符  -->
	<select id="findEmployee" parameterType="Employee" resultMap="employeeMap">
        SELECT
        e.id,
        e.name ename,
        e.age,
        e.sex,
        d.name dname,
        a.account
        FROM  employee e
        LEFT JOIN dept d ON e.dept_id=d.id
        LEFT JOIN admin a ON e.admin_id=a.id
        <trim prefix="where" prefixOverrides="and">
            <choose>
                <when test="name!=null &amp; name!='' ">
                  and  e.name  =#{name}
                </when>

                <when test="age!=null &amp; age!='' ">
                  and  e.name='李四'
                </when>

                <otherwise>
                  and e.name='里利'
                </otherwise>
            </choose>
        </trim>
    </select>
```

当第一个when的条件成立时(name值不为空or空串)

![在这里插入图片描述](https://img-blog.csdnimg.cn/dc318f6792194f44942a51a550c11ba3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


当第二个when的条件成立时(age不为空时)

![在这里插入图片描述](https://img-blog.csdnimg.cn/902965dd2750454bb07b4b56b80efb2d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


当条件不成立时(执行otherwise里的语句)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d2e9879a89814bdf8f47eb31fec51dc3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


### foreach

主要用在构建In中

```mysql
SELECT * FROM employee WHERE age IN(20,22,24)
```

 可以在 SQL 语句中进行迭代一个集合。

主要属性:

- **item**: 表示集合中每一个元素进行迭代时的别名
- **index**: 指定一个名字，用于 表示在迭代过程中，每次迭代到的位置
- **open**: 表示该语句以什么开始
- **separator**: 表示在每次进行迭代之间以什么符号作为分隔符
- **close**: 表示以什么结束

- **collection**: 该属性是必须指定的，但是在不同情况下，该属性的值是不一样的
  -  如果传入的是单参数且参数类型是一个 List 的时候，**collection** 属性值为 **list**
  -  如果传入的是单参数且参数类型是一个 array 数组的时候， **collection** 的属性值为 **array**

1. 使用集合作为参数,测试foreach的效果

```java
	//根据年龄集合查找年龄范围内的员工
    List<Employee> findEmployeeByAge(List<Integer> list);
```

```xml
	<select id="findEmployeeByAge" resultType="Employee">
        select * from employee where age in
        <foreach item="age" collection="list" open="(" separator="," close=")">
            #{age}
        </foreach>
    </select>
```

```java
	@Test//传入集合测试foreach
    public void find4(){
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        List<Integer> list = new ArrayList<>();
                      list.add(20);
                      list.add(21);
                      list.add(22);
              mapper.findEmployeeByAge(list);
        sqlSession.commit();
        sqlSession.close();
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/81a0717fdf4d44a9a05b16210caa44c2.png#pic_center)


2. 使用数组作为参数,测试foreach的效果

```java
    //根据年龄数组查找年龄范围内的员工
    List<Employee> findEmployeeByAge1(Integer[] arr);
```

```xml
	<select id="findEmployeeByAge1" resultType="Employee">
        select * from employee where age in
        <foreach item="age" collection="array" open="(" separator="," close=")">
            #{age}
        </foreach>
    </select>
```

```java
 	@Test//传入数组测试foreach
    public void find5(){
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        Integer[] arr = new Integer[]{20,21,22};
        mapper.findEmployeeByAge1(arr);
        sqlSession.commit();
        sqlSession.close();
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d3466d13f3c14ac0937171f2e41726c8.png#pic_center)
