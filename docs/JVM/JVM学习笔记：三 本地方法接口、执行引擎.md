

# 本地方法接口
![在这里插入图片描述](https://img-blog.csdnimg.cn/570d8759e421497c85c94fcc3f366306.png)


## 本地方法

- Java中的native修饰的方法
- native method 就是java调用非java代码的接口
- 本地方法都是操作系统提供的（由C或C++编写）
- 定义native方法，不提供实现，调用的是非Java代码实现
- **native可以与除abstract之外的标识符连用**

## 使用本地方法的好处
1. Java可以与底层操作系统交互
	 有些任务使用Java不容易实现
	不必关心实现细节，只要调用即可
2. Java与硬件进行数据交互(    `start()`, `read()`, `hashCode()`   )
	JVM的解释器用C实现，可以更好地与外部交互
	jre大部分是Java实现，通过一些本地方法来和外界交互
	例如 `java.lang.Thread` 的 `setPriority()`方法是用 Java 实现的，但是它实现调用的事 该类里的本地方法 `setPriority0()`
	![在这里插入图片描述](https://img-blog.csdnimg.cn/bdcafba37f194a579d160cbe53974937.png)
	![在这里插入图片描述](https://img-blog.csdnimg.cn/8cb9bc68050d447a9f586f007ae981a0.png)



# 执行引擎
## 概述
执行引擎是JVM的核心组成部分之一

![在这里插入图片描述](https://img-blog.csdnimg.cn/9a357d8322064f368688201a8a232905.png)
JVM将字节码装载到内存，字节码不是可执行的机器码，需要**经过执行引擎将字节码解释/编译为对应平台上的机器指令**。

执行引擎在JVM中扮演一个将高级语言翻译为机器语言的角色

- 前端编译：.java -->  .class
- 后端编译：.class 解释/编译为机器码

## 执行引擎的机制
1. 解释器
	JVM启动时，根据预定义的规范对字节码逐行地解释执行，将字节码内容翻译为对应平台的机器码执行。
2. JIT编译器
	将字节码整体编译为机器码，但不立即执行
	
## 为什么Java是半解释半编译型语言？
JIT即时编译器是Java后来有的，原先就是解释执行

1. 原来单纯的逐行解释字节码执行的效率低
2. JIT将字节码翻译为机器码后，缓存到方法区的JIT代码缓存中，提升了执行效率
3. 根据代码被执行的频率来决定是否启动JIT编译器将字节码直接编译为对应平台的机器码

JVM会针对使用频率较高的热点代码进行编译，缓存到方法区，执行效率高

### JIT 编译器执行效率高为什么还需要解释器？
JIT编译器编译过程需要时间，但是执行效率高。

程序一启动，解释器马上响应，去解释执行。

将两者结合，JVM启动后，先通过解释器解释执行代码，之后使用编译器编译执行。采用解释器和即时编译器并存的架构来平衡。

