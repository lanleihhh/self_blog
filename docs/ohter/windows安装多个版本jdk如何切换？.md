
# windows安装多个版本jdk如何切换？
## 1. 配置JAVA_HOME
配置多个版本的JAVA_HOME

![在这里插入图片描述](https://img-blog.csdnimg.cn/c8a0c7e226e348cf917c2cab7e3ab669.png)

配置JAVA_HOME为`%?%`，?为设置的jdk对应的JAVA_HOME变量名
![在这里插入图片描述](https://img-blog.csdnimg.cn/861c6afa30544aebabf8444cd117457d.png)
## 2. 配置CLASSPATH
在变量CLASSPATH中添加

```java
.
%JAVA_HOME%\lib
%JAVA_HOME%\lib\tools.jar
```
这三个变量
![在这里插入图片描述](https://img-blog.csdnimg.cn/8f297644e9804c9a8eb54645f951c7c7.png)
第一个变量值设为盘符类型，可以将多个变量值以列表呈现，否则就是`值1;值2;值3;`这样
![在这里插入图片描述](https://img-blog.csdnimg.cn/25463af793644b6da8e9fd9be14e5b56.png)
## 3. 配置Path
添加这两个变量值
![在这里插入图片描述](https://img-blog.csdnimg.cn/55c34f2090b74616ac1eec5f1b752722.png)

## 切换jdk
只需要修改JAVA_HOME的值(配置的jdk变量名)![](https://img-blog.csdnimg.cn/4302060db4854a5a8e117a7cab31906c.png)


