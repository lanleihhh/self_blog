# Maven概述与搭建

## 一.Maven概述

### Maven是什么?

**Maven**是**Apache**提供的一个开源项目,是一个项目==**构建**==工具,可以帮助开发者管理项目中的jar,以及jar之间的依赖关系,完成项目的==**编译**==,**==测试==**,==**打包**==,**==发布==**等工作.



### Pom

**Pom**(Project Object Model 项目对象模型) 

Maven管理的项目的根目录下都有一个 **pom.xml** 文件。

pom.xml 文件**指示 Maven 如何工作**。 

在 pom.xml 文件中**配置**项目==基本信息==以及项目==构建信息==等。比如：**项目坐标**、 **项目依赖的 jar**、**插件**、**编译选项**等。 一旦在 pom.xml 文件中配置了所依赖的 jar，Maven 会自动从构件仓库中下 载相应的构件。 

### 项目坐标

 maven 给每个 jar 定义了唯一的标志，这个在 maven 中叫做项目的坐标，通过这个坐标可以找到你需要用到的任何版本的 jar 包。 **groupId**、**artifactId**、**packaging**、**version** 的组合被称为项目的坐标，它们形成了项目的唯一标识，Maven 通过**坐标**来精确**定位构件**。其中 **groupId、artifactId、version 是必须**的，且这三项的值必须唯一，packaging 是可选的(默认为 jar)。 

例如:

```xml
	<groupId>com.company</groupId>
    <artifactId>mavenPro</artifactId>
    <version>1.0</version>
    <name>mavenPro</name>
	<packaging>war</packaging>
```



### 仓库

存放jar包的地方,

分类:

- 中央仓库(只有一个 apache创建的)
- 镜像仓库(可以有多个,对中央仓库的备份)
- 本地仓库(在自己电脑上)

![在这里插入图片描述](https://img-blog.csdnimg.cn/df5caa8787f84659b25a3b28e026557c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 二.Maven开发环境搭建

前提:安装Maven需要有jdk

### 搭建步骤:

1. 安装jdk

2. 下载 maven 服务器 (https://maven.apache.org/)
   
- 点击左侧导航栏download,选择版本
   
3. 安装,配置maven
   -  点击下载二进制的 apache-maven-3.5.2-bin.zip 进行解压 
   -  在系统环境中配置环境变量，在path中配置到maven的bin目录 
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/a1b671e2ba6c4b018fe2913e0cefb796.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


4. 在cmd中输入mvn -version查看其版本
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/d691ea69e9e8450981a2462077cb7bbc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

   - 如上图所示,表示maven环境配置成功了

5. 配置本地仓库地址

   - 找到解压后的maven文件夹中的conf文件夹的setttings.xml文件对其修改

     - 修改仓库地址

     新建一个存储jar包的文件夹,将其地址存入localRepository

     ```xml
     <localRepository>本地仓库地址</localRepository>
     ```

     

     - 配置阿里云maven镜像仓库(下载速度更快,不修改也可)

       默认地址

     ![在这里插入图片描述](https://img-blog.csdnimg.cn/adeb42a5a6844349a5f8cb663f566e15.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


     ​	阿里云镜像地址(替换掉默认地址)

     ```xml
     <mirror>
         <id>alimaven</id>
         <name>aliyun maven</name>
         <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
         <mirrorOf>central</mirrorOf>
     </mirror>
     ```

### 在idea中创建Maven项目

![在这里插入图片描述](https://img-blog.csdnimg.cn/2af8218d2d804ffd84afe33b77081d60.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/04b48c70f8784dd5beecd9aab380f7b4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


创建一个Maven框架的项目

选择Java Enterprise

![在这里插入图片描述](https://img-blog.csdnimg.cn/91cc8e02365d452288b128e12b359e5a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


勾选web选项

![在这里插入图片描述](https://img-blog.csdnimg.cn/29e5e0631ceb43058c59d0cd0651ed04.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



创建好项目后, maven会过网络下载各种插件以完成任务，因此应保证网络的通畅

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/0ee468a9d3374019b159d26f6aa927fa.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


项目自动将所需要的jar包等都下载到了指定的本地仓库mavenRepository中

![在这里插入图片描述](https://img-blog.csdnimg.cn/e83233ace41445a383ae9e6431220732.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


### Pom.xml配置

 maven 仓库官网(http://mvnrepository.com/)如下图所示: 

![在这里插入图片描述](https://img-blog.csdnimg.cn/7c23a96fa78d43d3b03fa993ffb14cdc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


 在 Pom.xml 文件配置  jar 包信息，使用 maven 自动下载 jar 包 

配置下载上传组件

```xml
<!-- 文件下载上传组件 -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

配置servlet

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```



pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--项目基本信息配置-->
    <groupId>com.company</groupId>
    <artifactId>mavenPro</artifactId>
    <version>1.0</version>
    <name>mavenPro</name>
    <packaging>war</packaging>

    <properties>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
        <junit.version>5.6.2</junit.version>
    </properties>

    <!--配置项目中依赖的jar的坐标-->
    <dependencies>
        <!--文件上传下载组件-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!--servlet-->
        <dependency>
            <!-- groupId: 组id,路径-->
            <groupId>javax.servlet</groupId>
            <!-- 构建名=项目名 -->
            <artifactId>javax.servlet-api</artifactId>
            <!-- version: 版本号 -->
            <version>3.1.0</version>
            <!--type:依赖项的packaging类型  不写默认为jar-->
            <type>jar</type>
            <scope>provided</scope>
            <!-- scope:依赖项作用范围
             取值:compile: 会被打包到war包中
                 provided:不会被打包
             默认值compile -->
        </dependency>
    </dependencies>

    <build>
        <!--配置插件-->
        <plugins>
            <!--配置maven打包插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.0</version>
            </plugin>
        </plugins>
    </build>
</project>
```

配置完,点击按钮下载组件

![在这里插入图片描述](https://img-blog.csdnimg.cn/da86b4dad0eb420085f56ab8d1bb8383.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_13,color_FFFFFF,t_70,g_se,x_16#pic_center)


子元素:

```xml
依赖项的基本坐标
<groupId>路径
<artifactId>与项目名相同
<version>版本
<type>依赖项的 packaging 类型，默认是 jar
<scope>依赖项的作用范围，默认是 compile。各个取值及含义如下：
compile：编译时和运行时依赖。如果你的项目最终打包为 war 包，则该依赖包会被拷贝到 war 包中的 lib 目录中。
provided：编译项目主代码和测试代码时使用此依赖项，运行时由 jre 或 web容器提供。如果你的项目最终打包为 war 包，则该 jar 
```

### Maven命令

Maven 常用命令 

1. **compile**    ---- 编译 
2.  **clean**       ----- 删除 target 
3.  **test**          -----test case junit/testNG 
4. **package**   --- 打包 
5. **install**       --- 把项目 install 到本地仓库 

###  添加 jar 到 maven 仓库 

```xml
mvn install:install-file
-DgroupId=组名(com.company) 
-DartifactId=构建名(项目名) 
-Dversion=版本(1.0) 
-Dpackaging=包类型(jar/war) 
-Dfile=jar 包所在的完整路径

示例
mvn install:install-file -DgroupId=com.company -DartifactId=gson-2.1.jar -Dversion=1.0 -Dpackaging=jar -Dfile=E:\user\course\javaEE\gson-2.1.jar
```

将命令在cmd中运行,若显示 build sucess 则表示添加成功


