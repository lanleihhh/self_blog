# 视图

## 概述

- **视图**是基于查询的**虚拟表**。通俗的理解，视图就是一条 **SELECT 语句执行后返回的结果集**。
- SELECT 语句所查询的表称为视图的基表，而查询的结果集称为虚拟表，**视图本身并不存储具体的数据**，视图的数据存在于视图的基表中，基本表数据发生 了改变，视图的数据也会跟着改变。

## 使用视图的好处

使用视图是为了方便复杂的查询语句。基本思路是将复杂的查询语句定义在 视图内部，然后对视图进行查询，从而简化复杂的查询语句。

## 视图的语法

- **定义视图**
  ```mysql
  -- 将select 查询到的结果包装在视图中,可以重复使用
  CREATE VIEW 视图名 AS select查询语句;
  ```
  在sqlyog工具中,可以直接鼠标右击视图项-->创建视图-->输入视图名,生成一个模板,效果如下
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/be5900aa07654b99a2c9c39c49837528.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)


- **删除视图**
  ```mysql
  drop view 视图名
  ```
- **使用视图**
  ```mysql
  SELECT * FROM 视图名
  ```

## 示例代码

```mysql
-- 创建视图(查询"每个管理员对应的角色"的结果作为视图)
CREATE VIEW view_role AS
SELECT r.id,
   a.account,
   r.name
FROM ROLE r
	 LEFT JOIN admin_role ON r.id = role_id
	 LEFT JOIN admin a ON admin_id = a.id

-- 使用视图
SELECT * FROM view_role

-- 删除视图
DROP VIEW view_role
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/65ec19eeef00447989022fe7b9353b01.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_15,color_FFFFFF,t_70,g_se,x_16)
> 总结:视图就是将一个较为复杂的**查询**语句的**结果包装在视图中**,简化语句,视图被存储在数据库中,可以**重复使用**,**视图**中并**不存储数据**

# 存储过程

## 概述

> 将一段逻辑处理的语句存储在数据库里，使用的时候由JDBC调用，减少了应用程序与数据库的交互，在数据库的内部执行，效率高


如果实现用户的某些需求时，需要编写一组复杂的 SQL 语句才能实现，那么 可以将这组**复

存储过程**类似**于 JAVA 语言中的**方法**，需要先定义，使用时需要调用。存储过程可以定义参数，参数分为 **IN、OUT、INOUT** 类型三种类型。

- **IN** 类型的参数表示接收调用者**传入的数据**
- **OUT** 类型的参数表示向调用者**返回的数据**
- **INOUT** 类型的参数既**可以**接收调用者**传入**的参数，**也可以**向调用者**返回**数据。

## 存储过程定义

- **创建存储过程**

```myql
DELIMITER $$ -- 开始位置
CREATE
    PROCEDURE 存储过程名（[in 变量名 类型，out 参数 2，…]）
	BEGIN
	  [declare 变量名 类型 [DEFAULT 值];]
	 存储过程语句块;
	END$$
DELIMITER; -- 结束位置
```

- **调用存储过程**

```mysql
CALL 存储过程名(参数列表);
```

- **删除存储过程**

```mysql
DROP PROCEDURE [ IF EXISTS ] <过程名>
```

解析:

- 存储过程的参数分为 **in**(入参)，**out**(出参)，**inout**(可出可入) 三种类型。
- **in** 代表**输入**参数（默认情况下为 **in** 参数），表示该参数的值必须由调用程序指定。
- **out** 代表**输出**参数，表示该参数的值经存储过程计算后，将 **out** 参数的**计算结果返回给调用程序**。
- **inout** 代表即是输入参数，又是输出参数，表示该参数的值即可以由调用程序 指定，又可以将 **inout** 参数的计算结果返回给调用程序
- 存储过程中的语句必须包含在 begin 和 end 之间。
- **declare** 中用来声明变量,变量**默认**赋值使用 **default**,语句块中**改变变量**值,使用 **set** 变量=值;

## 存储过程的使用

### 不带参数的存储过程

```mysql
DELIMITER $$
CREATE
    -- 在ssm数据库中创建一个名为demo1的存储过程,类似java中的方法,可以入参出参
    PROCEDURE `ssm`.`demo1`()
BEGIN
    -- DECLARE 声明变量
    DECLARE v_name VARCHAR(20) DEFAULT 'sql';
    SET v_name = 'mysql';
    -- 测试输出
    SELECT v_name;
END$$
DELIMITER ; -- 结束位置
```

调用存储过程

```mysql
CALL demo1();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f216c3ff0cde41deb17e11fab28fbfe7.png)

### 带参数的存储过程

```mysql
-- 定义一个有参数的存储过程
DELIMITER $$
CREATE PROCEDURE ssm.demo2(IN p_type INT, OUT p_count INT) -- IN 入参,OUT 出参
BEGIN
    -- 根据传入的新闻类型参数-->统计指定新闻类型的数量
    SELECT COUNT(*) INTO p_count FROM news WHERE TYPE = p_type;
    SELECT p_count; -- 查询出参结果
END$$
DELIMITER ;

-- @p_count 用来测试输出参数用,实际开发时不是这样
CALL demo2(2, @p_count); 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/43190bd2785545a49e80bec11b0ce1e8.png)

### 流程控制语句 if else

**语法:**

```mysql
    IF 表达式1 THEN 
	   表达式1成立时执行的语句;
ELSEIF 表达式2 THEN 
	   表达式2成立时执行的语句;
ELSE
	   都不成立时执行的语句;
END IF;    -- 结束if语句
```

**在存储过程中使用IF**

```mysql
-- 测试if语句的存储过程
DELIMITER $$
CREATE PROCEDURE `ssm`.`test_if`(IN p_day INT, OUT p_name VARCHAR(10)) -- IN 入参,OUT 出参
BEGIN
    IF p_day = 1 THEN
        SET p_name = '星期一';
        SELECT p_name;
    ELSEIF p_day = 2 THEN
        SET p_name = '星期二';
        SELECT p_name;
    ELSE
        SET p_name = '其他日期';
        SELECT p_name;
    END IF;
END$$
DELIMITER ;

-- 调用
CALL test_if(1, @p_day);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/db7107e2252e432cb3cd38c353d60042.png)

## mybaits调用存储过程

mysql代码

```mysql
-- 存储过程-->报存管理员,在存储过程中判断是否存在此账号,存在返回该账号,不存在就存到表中
DELIMITER $$
CREATE PROCEDURE `mybatis_db`.`is_exist`(IN p_account VARCHAR(12), IN p_password VARCHAR(12),
                                         OUT p_result VARCHAR(12)) -- IN 入参,OUT 出参
BEGIN
    -- 声明一个变量 , 接收查询到的结果
    DECLARE v_count INT DEFAULT 0;
    SELECT COUNT(*) INTO v_count FROM admin WHERE account = p_account;
    IF v_count = 0 THEN
        INSERT INTO admin(account, PASSWORD) VALUES (p_account, p_password);
        SET p_result = '保存成功!';
    ELSE
        SET p_result = '账号已存在!';
        SELECT p_result;
    END IF;
END$$
DELIMITER ;

-- 调用
CALL is_exist('1111', '111', @p_result);
```

java代码

```java
@Test
public void save(){
        SqlSession sqlSession=MybatisUtil.getSqlSession();
        AdminMapper mapper=sqlSession.getMapper(AdminMapper.class);

        Map<String, String> map=new HashMap<>();
        map.put("account","a01");
        map.put("password","111");
        mapper.save(map);
        System.out.println(map.get("result"));//获取输出参数

        sqlSession.commit();
        sqlSession.close();
        }
```

sql映射文件（AdminMapper.xml）-->必须指定jdbcType

```xml
    <!-- 必须指定jdbcType
	property:java参数 jdbcType:数据库数据类型  mode:IN/OUT 表示入参/出参-->
<parameterMap id="saveMap" type="hashmap">
    <parameter property="account" jdbcType="VARCHAR" mode="IN"></parameter>
    <parameter property="password" jdbcType="VARCHAR" mode="IN"></parameter>
    <parameter property="result" jdbcType="VARCHAR" mode="OUT"></parameter>
</parameterMap>

        <!-- 测试存储过程 -->
<insert id="save" parameterMap="saveMap" statementType="CALLABLE">
<!-- call 存储过程名()  调用存储过程 -->
call is_exist(?,?,?);
</insert>
```

结果![在这里插入图片描述](https://img-blog.csdnimg.cn/44b742372bed47729a7cdd49a73d1250.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
改变参数,重新传一个账号密码
![在这里插入图片描述](https://img-blog.csdnimg.cn/ecf8582aca0e4c9cb4bd54162e3c8047.png)
结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/c54f24d47005463e935aa9ae039ef16f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b407d1115b5d4225ba9124aa770e1e57.png)

# 函数

> 函数类似于存储过程,但是函数主要用于查询
> 函数只有入参，没有出参，必须有返回值，在一般的查询语句中调用

## 函数使用语法

- **创建**

```mysq
DELIMITER $$
CREATE FUNCTION 函数名([参数列表])  RETURNS 数据类型
    BEGIN
	DECLARE 变量； 
		sql 语句;
	 RETURN值;
    END $$
DELIMITER ;
```

- **删除**

```mysql
    DROP FUNCTION 函数名;
```

**使用注意事项**

1. 参数列表包含两部分：参数名 参数类型
2. 函数体：肯定会有 return 语句，如果没有会报错
3. 函数体中仅有一句话，则可以省略 begin end
4. 使用 delimter 语句设置结束标记

## 设置函数可以没有参数

```mysql
-- 全局设置--设置该函数可以没有参数
SET GLOBAL log_bin_trust_function_creators = TRUE
```

## 函数使用实例

### 无参函数

```mysql
-- 函数
DELIMITER $$
CREATE FUNCTION ssm.fun1() RETURNS INT
BEGIN
    -- 声明一个int变量,  变量以v_开始,v表示variable
    DECLARE v_count INT DEFAULT 0;
    -- 统计admin表中数据量总数
    SELECT COUNT(*) INTO v_count FROM admin;
    -- 返回总数
    RETURN v_count;
END $$
DELIMITER ;

-- 调用
SELECT fun1();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/8fde8032805d456d8cf50399ef1d1cff.png)

### 有参函数

**(1)写一个区分超管与管理员的函数**

```mysql
--  写一个区分超管与管理员的函数
DELIMITER $$
CREATE FUNCTION ssm.find_type(p_type INT) RETURNS VARCHAR(10)
BEGIN
    DECLARE v_type VARCHAR(10) DEFAULT '';
    IF p_type = 0 THEN
        SET v_type = '超级管理员';
    ELSE
        SET v_type = '管理员';
    END IF;
    RETURN v_type;
END$$
DELIMITER ;

-- 调用  (查询amdin时,将admin的类型id对应的类型名称查询到)
SELECT account, id, find_type(TYPE)
FROM admin
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/26f7fee5d99b4433b8969da2dfe3d8a1.png)
**(2)创建一个查询新闻类型的函数**

```mysql
-- 创建一个查询新闻类型的函数 
DELIMITER $$
CREATE FUNCTION find_news_type(p_typeid INT) RETURNS VARCHAR(10)
BEGIN
    DECLARE v_type VARCHAR(10) DEFAULT '';
    SELECT NAME INTO v_type FROM news_type WHERE id = p_typeid;
    RETURN v_type;
END$$
DELIMITER ;

-- 调用
SELECT id, title, find_news_type(TYPE)
FROM news;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/afb01650e1f2418d869b8a4752db3338.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_18,color_FFFFFF,t_70,g_se,x_16)

# 触发器(trigger)

## 概述

触发器（trigger）是一种特殊的存储过程，它**不需要直接调用**，在满足某种条件时执行-->
**对表操作**(添加、修改、删除)**之前或之后自动执行**。
> 类似于Java/JavaScript中的事件,在指定条件触发时执行

## 触发器的特点

1. **与表相关联**
   触发器定义在特定的表上，这个表称为触发器表。

2. **自动激活触发器**
   当对表中的数据执行 INSERT、UPDATE 或 DELETE 操作时，如果对表上的这个特定操作定义了触发器，该触发器自动执行，这是不可撤销的。

3. **不能直接调用**
   与存储过程不同，触发器不能被直接调用，也不能传递或接受参数。

4. **作为事务的一部分**
   触发器与激活触发器的语句一起做为对一个单一的事务来对待，可以从触发器中的任何位置回滚。

## 触发器语法

```mysql
    -- 触发器
DELIMITER $$
CREATE TRIGGER 触发器名称 触发时机 触发事件
    ON 表名称
    FOR EACH ROW -- 行级触发器  操作多行时,每行都会触发 '触发器'
BEGIN
    执行的sql语句;
END$$
DELIMITER ;
```

**语法解析**

1. **触发器名称**：是用来标识触发器的，由用户自定义。
2. **触发时机**：其值是 before 或 after。
3. **触发事件**：其值是 insert，update 和 delete
4. **表名称**：标识建立触发器的表名，即在哪张表上建立触发器
5. **语句**：是触发器程序体，触发器程序可以使用 begin 和 end 作为开始和结束， 中间包含多条语句；

## 案例1--删除admin时,自动触发删除admin与role的关系

```msql
-- 触发器
DELIMITER $$
CREATE TRIGGER ssm.delete_admin_role 
    BEFORE DELETE
    ON admin
    FOR EACH ROW -- 行级触发器  操作多行时,每行都会触发 '触发器'
    BEGIN
	DELETE FROM admin_role WHERE admin_id = old.id; -- old 旧的数据
    END$$
DELIMITER ;

-- 删除之前会触发触发器删除admin与role表的关联关系
DELETE FROM admin WHERE id = 39
```

## 案例2--新增admin时，自动向admin_log表插入数据

```mysql
-- 向admin表插入数据时,往admin_log中插入记录
DELIMITER $$
CREATE TRIGGER save_admin_log
    AFTER INSERT
    ON admin
    FOR EACH ROW -- 行级触发器  操作多行时,每行都会触发 '触发器'
BEGIN
    INSERT INTO admin_log(id, account, oper_time) VALUES (new.id, new.account, NOW());
END$$
DELIMITER ;

INSERT INTO admin(account, PASSWORD)
VALUES ('b01', '111')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/0378a00846a64aa9897e5d40598a6827.png)

