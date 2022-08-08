
@[TOC](Nginx入门)
# 概述
- Nginx 是一款是由**俄罗斯**的程序设计师所开发高性能的 **Web** 和 **反向代理** 服务器，也是一个 IMAP/POP3/SMTP 代理服务器。 
- 其特点是占有**内存少，并发能力强**(支持同时**5万**的访问量,**tomcat只有几百**)，事实上 nginx 的并发能力在同类型的 网页服务器中表现较好，中国大陆使用 nginx 网站用户有：百度、京东、新浪、 网易、腾讯、淘宝等。 
- Nginx 专为性能优化而开发，性能是服务器最重要的考量，实现上非常注重 效率，能经受高负载的考验，据报告能支持高达 50,000 个并发连接数。 在高连接并发的情况下，Nginx 是 Apache 服务器不错的替代品。 
- Nginx 不仅能做**反向代理，实现负载均衡**；还能可以作**正向代理来进行上网等功能**。

# 代理服务器
- 所谓代理服务器就是位于发起请求的客户端与原始服务器端之间的一台**跳板服务器**(类似于购车时不直接与厂家沟通,而是通过4S店的工作人员向厂家提车,厂家也不直接与客户沟通)，正向代理可以隐藏客户端，反向代理可以隐藏原始服务器。

# 正向代理
- 用户知道目标服务器地址，但由于网络限制等原因，无法直接访问。这时候需要 先连接代理服务器，然后再由代理服务器访问目标服务器。
![在这里插入图片描述](https://img-blog.csdnimg.cn/e3ca9604ee1b4d8b92e0c6e0eb573f7d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

# 反向代理
![在这里插入图片描述](https://img-blog.csdnimg.cn/c435b49f088e413985003ce4cd525e22.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

- 反向代理对用户则是不可知的，比如我们访问百度网站，百度的代理服务器对外的域名为 www.baidu.com ,具体内部的服务器节点我们不知道，现实中我们通过访问百度的代理服务器后，代理服务器给我们转发请求到他们 N 多的服务器节点中的一个给我们进行搜索后将结果返回。
- 我们ping一下百度的地址
![在这里插入图片描述](https://img-blog.csdnimg.cn/6ad0082cb1b0471fb39a87f60d8b3c60.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
- 与浏览器中访问百度的服务器地址并不相同,可知并不是同一台服务器,百度后台有多台服务器.![在这里插入图片描述](https://img-blog.csdnimg.cn/add701b5502047ddb7f3939d2c197d58.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_12,color_FFFFFF,t_70,g_se,x_16)
# 负载均衡
- 客户端发送多个请求到服务器，服务器处理请求，有些可能要访问数据库，服务 器处理完毕后再将结果返回客户端。 
- 这种架构模式单一，适合并发请求少的情况，但并发量大的时候如何解决？ 
- 此时想到服务器集群，增加服务器数量，然后将原先请求单个服务器的情况改为 将请求分发到多个服务器上，将负载分发到多个服务器上.

![在这里插入图片描述](https://img-blog.csdnimg.cn/4bc70488bbce442da3f13a069ca6be01.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
## 负载均衡的调度算法
1. **轮询**
	按时间顺序逐一分配到不同的后端服务器。 
2. **加权轮询** 
	可在配置的 server 后面加个 weight=number，number 值越高，分配的 概率越大。
3. **ip_hash** 
每个请求按访问 IP 的 hash 分配，这样来自同一 IP 固定访问一个后台服务 器。
4. **least_hash** 
最少链接数，哪个机器连接数少就发分发给哪个机器。





# 动静分离
- Nginx 是一个静态资源服务器,为了加快网站的解析速度，可以把动态页面和静 态页面有不同的服务器来解析，减少服务器压力，加快解析速度。将 java 后端 程序部署在独立的服务器上,nginx 代理访问后端服务. 
- 实现动态请求与静态请求分离,实现资源分类

![在这里插入图片描述](https://img-blog.csdnimg.cn/9c67380b21e44510b3bd08a9264b63c1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)


# 下载
地址:
```java
http://nginx.org/en/download.html
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/828f5d21255e4572becbc6291bd53e12.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

# windows安装
下载的是压缩文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/058e8cfcb3344a07a20b414448b23a72.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_14,color_FFFFFF,t_70,g_se,x_16)
启动
- 进入nginx的安装目录,启动cmd
![在这里插入图片描述](https://img-blog.csdnimg.cn/094ea0937f8a4428bf007f9da02046c0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
输入命令`nginx`或点击`nginx.exe`启动
![在这里插入图片描述](https://img-blog.csdnimg.cn/a12e2e67451d42b7979308c7f89480d5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
看到光标在闪没有报错,就是启动成功了
启动后访问 `http://localhost:80` (默认端口为80,可以在conf/nginx.conf中修改)

我将端口修改为了8088
![在这里插入图片描述](https://img-blog.csdnimg.cn/d6a1631c3e1f4db7b58b4e1e267218b6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
显示如上图,说明nginx没有问题

# 命令
- `nginx`  启动
- `nginx -s reload`  重载配置文件
- `nginx -s quit` 停止
- `nginx -s stop` 停止
# 配置文件
nginx 文件结构

```java
... #全局块 
events { 
    #events 块 
}
http { #http 块 
       ... #http 全局块 
    server{     #server块 
       ...        #server 全局块 
        location [PATTERN] { #location 块 
           ...
        }
        location [PATTERN] { 
           ... 
        } 
    }
      server{ }....
}
```
1. 全局块：配置影响 nginx 全局的指令。一般有运行 nginx 服务器的用户组， nginx 进程 pid 存放路径，日志存放路径，配置文件引入，允许生成 worker pr ocess 数等。 
2. events 块：配置影响 nginx 服务器或与用户的网络连接。有每个进程的最大 连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接， 开启多个网络连接序列化等。 
3. http 块：可以嵌套多个 server，配置代理，缓存，日志定义等绝大多数功能 和第三方模块的配置。如文件引入，mime-type 定义，日志自定义，是否使用 s endfile 传输文件，连接超时时间，单连接请求数等。 
4. server 块：配置虚拟主机的相关参数，一个 http 中可以有多个 server。 
5. location 块：配置请求的路由，以及各种页面的处理情况。

## 负载均衡配置实例

```conf
	upstream mytomcat{
		server localhost:9999 weight=1;
		server localhost:8888 weight=1;
	}
	
    server {
        listen       8088;
        server_name  localhost;
        
        location / {
            root   html;
            index  index.html index.htm;
        }
		
		location /api {
            proxy_pass http://mytomcat;
        }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/db40ed31dfb54b7ba4089c3884b1a4cc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
### 部署java后端
打包两个了java后端项目,端口号分别为9999和8888
idea打包步骤:
1. 修改端口,一个端口就是一个独立的程序
![在这里插入图片描述](https://img-blog.csdnimg.cn/1fc38c990a87474590aa7a875d713a50.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_19,color_FFFFFF,t_70,g_se,x_16)
2.使用maven工具打包package
![在这里插入图片描述](https://img-blog.csdnimg.cn/a3907283b90c411f8ed0884769d7bd98.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
选择package
![在这里插入图片描述](https://img-blog.csdnimg.cn/6a43bdadcb384833830b941bbd71d650.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/8e388a10e93c408182ee42b7c4902181.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
打包好的两个jar文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/5dc350adf2c34cb283e4b7a9c00d0ec2.png)
cmd 进入到存放jar的目录
输入命令

```java
java -jar jar包名.jar
如
java -jar backserver9999.jar
java -jar backserver8888.jar
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/3e75970fa5d542e990c31c4f837d0003.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
### 部署vue前端
nginx服务器是代理访问,vue项目中配置的跨域访问必须关掉
![在这里插入图片描述](https://img-blog.csdnimg.cn/2df55fb359684804bb249a629fc82835.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
为了能够正常访问,须加上`/api`这级目录,我们在每个请求地址前都加一个/api/![/](https://img-blog.csdnimg.cn/0021bce4f8a246ca99a1960132b17765.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
将项目打包
- 方式1:控制台命令 `npm run build` 
- 方式2:右击项目->外部命令->npm run build
![在这里插入图片描述](https://img-blog.csdnimg.cn/ee3978f91dc040fd8a6e89f966c383ca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_12,color_FFFFFF,t_70,g_se,x_16)

将dist目录中的所有文件复制到nginx安装目录中html文件夹里
(删除html中原来的文件)

![在这里插入图片描述](https://img-blog.csdnimg.cn/cdd718d84d3742c0a0f39b3bed185f95.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
启动nginx--命令`nginx`
![在这里插入图片描述](https://img-blog.csdnimg.cn/16efc963dc4340358190ea807ed3e0e9.gif#pic_center)
可以看出基本是交替访问的,因为在配置中我们设置两个端口权重都是1,某一个服务器分配的权重越大,经它访问的概率越大
