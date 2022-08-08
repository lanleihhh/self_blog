
# 安装jdk
## 下载.tar.gz文件

```java
cd /usr/local
mkdir java
```

使用xftp上传到`/usr/local/java`目录
## 解压jdk文件
（使用==tar –zxvf==命令）

```java
tar –zxvf jdk-8u261-linux-x64.tar.gz
```
## 配置环境变量
方式1：使用vim编辑器
```java
vim /etc/profile
```
方式2：
使用xftp打开`/etc/profile`目录使用记事本打开
![在这里插入图片描述](https://img-blog.csdnimg.cn/d870e8975e13479e86c4a5e4a907f825.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
在文件中加入如下代码

```java
export JAVA_HOME=/usr/local/java/jdk1.8.0_261 
export PATH=$PATH:$JAVA_HOME/bin
```
==java后面的jdk版本与解压的文件版本一致==
## 使修改后的文件生效
```java
source /etc/profile
```
## 检查jdk是否安装好

```java
javac
java -version
```

# 安装tomcat
## 下载.tar.gz文件
```java
cd /usr/local
mkdir tomcat
```
使用xftp上传到`/usr/local/java`目录
## 解压tomcat压缩文件
（使用==tar –zxvf==命令）

```java
tar -zxvf apache-tomcat-9.0.37.tar.gz
```
## 验证
进入 `/usr/local/tomcat//usr/local/tomcat/apache-tomcat-9.0.37/bin` 目录 

```java
./startup.sh 启动服务器 
./shutdown.sh 关闭服务器
```
如图，已经启动
![在这里插入图片描述](https://img-blog.csdnimg.cn/1713f0e0febb4fcdb9d3eaba2b15204a.png)

现在去浏览器访问：`公网ip：tomcat端口`

访问浏览器之前，要先关防火墙

```java
systemctl start firewalld.service            #启动防火墙  
systemctl stop firewalld.service             #停止防火墙  
systemctl status firewalld                   #查看防火墙状态
systemctl enable firewalld                   #设置防火墙随系统启动
systemctl disable firewalld                  #禁止防火墙随系统启动
```
如图防火墙开启
![在这里插入图片描述](https://img-blog.csdnimg.cn/98ea2723011a49a9b8c474372842d83c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

如图：防火墙关闭
![在这里插入图片描述](https://img-blog.csdnimg.cn/abc3933e03f84f029aa6c9f91c81ce98.png)
访问tomcat
![在这里插入图片描述](https://img-blog.csdnimg.cn/c1e0a8ef72b64f97a09c24e11b3002ab.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
# 安装mysql

检测系统是否自带安装 mysql

```java
yum list installed | grep mysql
```
给 CentOS 添加 rpm 源，选择与项目使用的mysql对应的版本

```java
wget https://dev.mysql.com/get/mysql80-community-release-el8-1.noarch.rpm
```
安装刚才下载的 rpm 文件

```java
yum install mysql80-community-release-el8-1.noarch.rpm
```

输入y
![在这里插入图片描述](https://img-blog.csdnimg.cn/f80e860a860a4458925a7df6f0e174c7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_19,color_FFFFFF,t_70,g_se,x_16)
使用 yum 安装 mysql

```java
yum install mysql-server
```
查看当前是否为开机服务

```java
systemctl list-unit-files|grep mysql
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/aaa5138a61cd40a6bea1ffe6658e5386.png)
disabled表示开机服务未开启

设置为开机启动

```java
systemctl enable mysqld.service
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/3587e3ec83e14aef8d54b5b1cf6593e7.png)
查看当前是否启动 MySQL 服务
```java
ps -ef|grep mysql
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/63080e834359408c9953e3f30a81e266.png)
启动 mysql 服务

```java
systemctl start mysqld.service
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/f888c7218d4744e3b3ed99caa068aa9c.png)
登录设置

```java
mysql -uroot -p
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/626bd08b814f4f7cb0f235aae5b51b28.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

查看信息
```java
show databases;
use mysql
select host,user from user;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/076102eea8574d0bb522fc66ebbcac24.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
看到本地主机账号为root

设置其他 ip 可以访问（设置后可以使用公网ip连接）

```java
update user set host='%' where user='root';
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d419c360b69b4ab18fbd88112d901d7d.png)
设置密码及加密格式

```java
 ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'lanlei6843';
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/19c369cc4e534ad294d502cbd03cbb77.png)
刷新指令

```java
FLUSH PRIVILEGES;                         --刷新权限
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/a05663a4fb7a4d1f9b0f5a479e4d2d61.png)

MySQL连接到linux
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7c442d3ca454f649b04bd55955cf793.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/741bde103aed47e180494c672b8862b2.png)
导入数据库

在Linux中测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/5f72c90cef2345a18fbb3c6d2bd79041.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
至此，环境已经安装完成。


