
# 第一步:在linux中安装java运行环境

可参考: [Linux安装Java环境](https://blog.csdn.net/lanleihhh/article/details/123789589?spm=1001.2014.3001.5501)



# 第二步: 修改本地代码
相较于本地代码,需要改动的地方:
**本地代码**:
保存图片的地址:

```java
File f0 = new File("E:\\百度云\\team\\src\\main\\webapp\\back\\images\\materiaMedica");
```
访问图片的地址:

```java
<img src='http://127.0.0.1:8080/images/materiaMedica/"+res.data[i].img+"'  />
```

数据库链接地址:

```java
jdbcUrl=jdbc:mysql://localhost:3306/team_db?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
```
**上传至Linux的代码**:
保存图片的地址:

```java
File f0 = new File("/usr/local/tomcat/apache-tomcat-9.0.37/webapps/team-1.0/back/images/materiaMedica");
```
访问图片的地址:

```java
<img src='http://122.112.196.221:8080/team-1.0/back/images/materiaMedica/"+res.data[i].img+"'  />
```
连接数据库地址:(前提是在Linux中创建了该数据库)

```java
jdbcUrl=jdbc:mysql://122.112.196.221\:3306/team_db?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
```

# 第三步: 将打好的war包放到linux中安装的tomcat的webapps文件夹下(使用xftp工具)
![在这里插入图片描述](https://img-blog.csdnimg.cn/dd166140ef034f7e972988f26488fddf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
打开xshell,进入tomcat的bin目录

```java
cd /usr/local/tomcat/apache-tomcat-9.0.37/bin
```
执行开启tomcat的命令 
```java
./startup.sh
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/0f7dbd9bd0c24c29bf9e11008b511d63.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
然后就可以在浏览器输入:ip:端口/项目war包名进行访问了


