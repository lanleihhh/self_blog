## 转义符

在 mybatis 中的 xml 文件中，存在一些特殊的符号，比如：<、>、"、'&、<> 等，正常书写 mybatis 会报错，需要对这些符号进行转义。具体转义如下所示： 特殊字符 转义字 

- `<`           `        &lt;`
- `>`           `&gt;`

- `"`           `&quot;`
- `'`           `&apos;`
- `&`           `&amp;`

```xml
	<!--查询年龄小于指定参数的员工-->
	<select id="findEmployeeByAge2" resultType="Employee">
        select * from employee where age &lt; #{age}
    </select>
```

```java
	@Test
	//找年龄<21的
    public void find6(){
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        mapper.findEmployeeByAge2(21);
        sqlSession.commit();
        sqlSession.close();
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/bd9d5e4c85634d0081a4ab2dac4e9069.png#pic_center)


除了可以使用上述转义字符外，还可以使用`<![CDATA[ ]]>`包裹特殊字符 . `<![CDATA[ ]]>`是 XML 语法。在 CDATA 内部的所有内容都会被解析器忽略。  

```xml
 	<select id="findEmployeeByAge2" resultType="Employee">
        select * from employee where age  <![CDATA[ < ]]> #{age}
    </select>
```

我们可以将需要转义才能被解析的字符放在`<![CDATA[ 特殊符号 ]]>`中



## 类型处理器（typeHandlers）  入参和查询结果 将java类型与数据库类型进行匹配转账
- BooleanTypeHandler	java.lang.Boolean, boolean	数据库兼容的 BOOLEAN
- ByteTypeHandler	java.lang.Byte, byte	数据库兼容的 NUMERIC 或 BYTE
- ShortTypeHandler	java.lang.Short, short	数据库兼容的 NUMERIC 或 SMALLINT
- IntegerTypeHandler	java.lang.Integer, int	数据库兼容的 NUMERIC 或 INTEGER
- LongTypeHandler	java.lang.Long, long	数据库兼容的 NUMERIC 或 BIGINT
- FloatTypeHandler	java.lang.Float, float	数据库兼容的 NUMERIC 或 FLOAT
- DoubleTypeHandler	java.lang.Double, double	数据库兼容的 NUMERIC 或 DOUBLE
- BigDecimalTypeHandler java.math.BigDecimal	数据库兼容的 NUMERIC 或 DECIMAL
- StringTypeHandler	java.lang.String	数据库兼容的 CHAR, VARCHAR
- ClobReaderTypeHandler	java.io.Reader	-
- ClobTypeHandler	java.lang.String	 
数据库兼容的  CLOB, LONGVARCHAR





































