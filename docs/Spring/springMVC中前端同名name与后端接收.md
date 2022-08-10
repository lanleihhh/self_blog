# springMvc--后端接收前端多个同名name标签值

提交表单时有多个name相同的input(checkbox...),后端如何来接收

![在这里插入图片描述](https://img-blog.csdnimg.cn/5b67b64765144fa7ab1fff6ac4200614.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


后端接收(学习Servlet时的做法--getParameterValues接收一组name值相同的value)

```java
	void accept(HttpServletRequest request){
        String[] roleId = request.getParameterValues("roleId");
    }
```

springMvc提供了更为方便的机制,我们只需在实体类中定义一个与name值相同的实体属性数组,springMvc会自动将一组name相同的一组数据封装到java实体属性中

```java
private Integer[] roleId;
//spring会将接收的name值相同的一组数据封装在model类中与name相同的数组变量中
```

这样提交一组name=''**roleId**"数据时,直接从对象的属性**roleId**中取

直接从对象的属性**roleId**中取

