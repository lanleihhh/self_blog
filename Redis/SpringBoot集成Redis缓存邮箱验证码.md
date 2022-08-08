
# 概述
Reids面向Java推出了Jedis，提供了与Redis命令对应封装的API，可以在Reids官网下载。

`spring-data-redis`提供了在spring中通过简单的配置去访问Redis服务。
对Redis底层开发包进行了高度封装，其中的`RedisTemplate`提供了Redis的各种操作。

`spring-data-redis`针对Jedis提供了这些功能：
1. 连接池自动管理,提供了高度封装了`RedisTemplate`类
2. 将同一数据类型的操作封装为`operation`接口
 	operation接口：
     - **ValueOperations**:简单的K-V操作
     - **HashOperations**：map类型操作
     - **ListOperations**：list类型操作
     - **SetOperations**：set类型操作
     - **ZSetOperations**:zset 类型操作
3. 针对数据的**序列化**，**反序列化**，提供了多种可选择的策略(**RedisSerializer**)
	1. **JdkSerializationRedisSerializer** 
		`POJO对象(实体类)存储取出使用JDK本身的序列化机制`
	2. **StringRedisSerializer** 
	`K 或 V 为string的场景，根据指定的编码格式charset将字节序列转为字符串`
	是`new String(bytes, charset)`和`string.getBytes(charset)`的封装
	是最轻量级和高效的策略
	
	3. **JacksonJsonRedisSerializer** 
	    `jackson-json`可以将**实体类**与**JSON**格式互相转换，存储在Redis中

# 搭建
## 1. 添加Reids依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
## 2. 配置Redis信息

```xml
spring:
  redis: # redis配置
    host: 122.112.196.221   # 服务器地址
    port: 6379              # Redi端口6379
    password: 123           # 在redis.conf里设置密码
    database: 0             # 选择一个数据库：0~15，默认使用0
    pool:
      max-active: 8         # 连接池最大连接数（使用负值表示没有限制）
      max-wait: -1ms        # 连接池最大阻塞等待时间（使用负值表示没有限制）
      max-idle: 8           # 连接池中的最大空闲连接
      min-idle: 0           # 连接池中的最小空闲连接
      timeout: 10000ms      # 连接超时时间（毫秒），取值范围为0~100000。默认值为0，表示无限制。
```
## 3. 注入RedisTemplate
```java
@Autowired 
RedisTemplate redisTemplate;
```
## 4. 测试
**新增，查询，删除**

```java
	@Autowired
    RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        //System.out.println(user);
        ValueOperations operations = redisTemplate.opsForValue();
        operations.set("name", "jim");//序列化为json格式 list对象
        Admin admin = new Admin();
        admin.setId(1);
        admin.setAccount("admin");
        admin.setPassword("123");
        operations.set("admin"+admin.getId(),admin,100, TimeUnit.MICROSECONDS);
        System.out.println("has: "+redisTemplate.hasKey("admin1"));
        System.out.println("admin1: "+operations.get("admin1"));
        System.out.println("name: "+operations.get("name"));
        System.out.println(redisTemplate.delete("admin1"));
        System.out.println(redisTemplate.delete("name"));

    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/25f96d4c5b2a45518be99ef88d79147a.png)
**序列化**
序列化配置类
```java
@Configuration
public class RedisConfig {//不让redis进行转码(会乱码)

    /**
     * 序列化 键:值
     * @param connectionFactory  是LettuceConnectionFactory的实例
     * @return
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(connectionFactory);
        //实体对象与json格式相互转换的序列化器
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
        //key-value为string的序列化器
        StringRedisSerializer redisSerializer = new StringRedisSerializer();
        redisTemplate.setKeySerializer(redisSerializer);
        redisTemplate.setHashKeySerializer(redisSerializer);
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
        return redisTemplate;
    }
}
```
# 邮箱验证
>授权码是QQ邮箱推出的，用于登录第三方客户端的专用密码。
>温馨提醒：为了你的帐户安全，更改QQ密码以及独立密码会触发授权码过期，需要重新获取新的授权码登录。
## 1.QQ邮箱开启 `POP3/SMTP服务`获取授权码
进入QQ邮箱，进入设置->账户![](https://img-blog.csdnimg.cn/25827606cc7f497492d4c3fb001eb223.png)

下拉    开启POP3/STMP服务
![在这里插入图片描述](https://img-blog.csdnimg.cn/f86be8d32e5344e5b6c681ad86341a8f.png)
验证密保
![在这里插入图片描述](https://img-blog.csdnimg.cn/ef2b6839583542e282e42e484b9f7799.png)
获取授权码
![在这里插入图片描述](https://img-blog.csdnimg.cn/3c8e2b5c2e95439c8291d64958b364ae.png)

## 2.在spingboot中配置
### 1.  添加依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```
### 2. springboot集成邮件

```xml
spring:
  mail:
    host: smtp.qq.com
    username: 2811662803             # 发送方qq号
    password: xhwyoxrqvtohddff       # 授权码
    default-encoding: utf-8
```

### 3. 注入 JavaMailSender 对象

```xml
@Autowired
private JavaMailSender mailSender;
```


###  4. 随机生成6位数字验证码

```xml
	Random random = new Random();
	//生成0-1000000之内的随机数
	int randomNum = random.nextInt(1000000);
	//不足6位补0
	String randomCode = String.format("%06d", randomNum);
```
### 5. 发送邮件的逻辑代码

```xml
  SimpleMailMessage message = new SimpleMailMessage();
					message.setFrom("发送方QQ号@qq.com");
					message.setTo("接收方QQ号@qq.com");
					message.setSubject("主题：简单邮件验证");
					message.setText("测试邮件内容");
					mailSender.send(message);
```

## 实例测试
### 发送验证码
#### 前端请求

```JavaScript
//发送验证码
sendYzm(){
	var _this = this;//this是vue对象
	
	//正则验证邮箱是否合法
	var email=this.form.account;
	var reg = /^\w{5,}@[a-z0-9]{2,3}\.[a-z]+$|\,$/;
	if(!reg.test(email)){
		_this.$message({
			message: "邮箱的格式不正确!",
			type: 'warning'
		});
	}

	this.$http.post("/api/login/sendYzm", this.form).then(function(response) {
		if (response.data.code == 201) {
			_this.$message({
				message: response.data.msg,
				type: 'warning'
			});
			return;
		}
		if (response.data.code == 200) {
			_this.$message({
				message: response.data.msg,
				type: 'success'
			});
			return;
		}
	})
}
```
#### 后端处理
Controller
```java
@PostMapping(path = "/sendYzm")
public CommonResult sendYzm(@RequestBody MailCode mailCode) {//@RequestBody 接收请求体数据
   service.createVerificationCode(mailCode.getAccount());
   return new CommonResult(200, "发送成功",null);
}
```
Service
```java
@Autowired
RedisTemplate redisTemplate;

@Autowired
JavaMailSender mailSender;

public void createVerificationCode(String account) {
	//创建Redis简单string的操作类
   ValueOperations operations = redisTemplate.opsForValue();
   
	//生成简单的6位验证码
   Random random = new Random();
   int randomNum = random.nextInt(1000000);
   String randomCode = String.format("%06d", randomNum);
	
	//发送邮件
   SimpleMailMessage message = new SimpleMailMessage();
   message.setFrom("2811662803@qq.com");
   message.setTo(account);
   message.setSubject("主题：简单邮件验证");
   message.setText("登录验证码："+randomCode);
   mailSender.send(message);

   //设置键值对  k-v = 邮箱:验证码  的有效时间
   operations.set(account, randomCode,1, TimeUnit.MINUTES);
}
```
### 校验 验证码
#### 前端请求
```html
//校验验证码
checkYzm(){
	var _this = this;//this是vue对象
	this.$http.post("/api/login/checkYzm",this.form).then(function(response) {
		console.log(response.data);
		if (response.data.code == 201) {
			_this.$message({
				message: response.data.msg,
				type: "warning"
			});
			return;
		}else if (response.data.code == 200) {
			_this.$message({
				message: response.data.msg,
				type: "success"
			});
			return;
		}else{
			_this.$message({
				message: response.data.msg,
				type: "warning"
			});
			return;
		}
	})
}
```
#### 后端处理
Controller
```java
	@PostMapping(path = "/checkYzm")
    public CommonResult checkYzm(@RequestBody MailCode mailCode) {//@RequestBody 接收请求体数据
        CommonResult res = null;
        boolean bool = false;
        try {
            bool = service.checkCode(mailCode.getAccount(),mailCode.getCode());
            res = new CommonResult(200, "验证成功",bool);
        } catch (Exception e) {
            res = new CommonResult(500, "验证失败",bool);
        }
        return res;
    }
```
Service

```java
	public boolean checkCode(String account, String yzm) {
        ValueOperations operations = redisTemplate.opsForValue();
        String compare = (String)operations.get(account);
        if(compare.equals(yzm)){
            return true;
        }
        return false;
    }
```
正则验证邮箱
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa6688874ae14481bd3eae40fa973402.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/82da4a2f929a453c850d02047fd5bf75.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/c95c4480d90340d3a2f4aa76d394e191.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8166be56862747b3a305bbb2e34b4e15.png)

