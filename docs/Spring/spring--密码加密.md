# 密码加密

## 1.apache的common组件中的DigestUtils

```xml
		<!-- https://mvnrepository.com/artifact/commons-codec/commons-codec -->
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.10</version>
        </dependency>	
```

使用

```java
//使用md5加密技术将字符串变为32位的16进制数
String password = DigestUtils.md5Hex("111");//698d51a19d8a121ce581499d7b701668
String password = DigestUtils.md5Hex("123456");//e10adc3949ba59abbe56e057f20f883e
```

|![在这里插入图片描述](https://img-blog.csdnimg.cn/49910feba036421297a8956056d28904.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



注意:此方式加密是不可逆的,验证时需要将输入的字符串使用`DigestUtils.md5Hex()`加密后与数据库中已加密的密码去比较

## 2.spring核心包的DigestUtils

![在这里插入图片描述](https://img-blog.csdnimg.cn/261a00b359f147c1b7610200d8a28b6d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


