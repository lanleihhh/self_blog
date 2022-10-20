
>虚拟机：Virtual Machine ，是一款软件，用来执行一系列计算机指令。大体上，虚拟机可以分为**系统虚拟机**和**程序虚拟机**。
>**VMware**属于系统虚拟机，是对物理计算机的仿真，提供了一个可运行完整操作系统的平台。
>**JVM** 属于程序虚拟机，它专门为执行某个计算机程序而设计。
# JVM概述
JVM（Java Virtual Machine）Java虚拟机
- 在JVM中执行的指令称为Java字节码指令
- JVM是一种执行Java字节码文件的虚拟计算机，它拥有独立的运行机制
- Java核心技术就是JVM，所有的Java程序都运行在JVM内部。

## JVM的作用
![在这里插入图片描述](https://img-blog.csdnimg.cn/65ea704aaeeb4b6a9d9671395dddaf6f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
## JVM 特点
- 一次编译，到处运行（跨平台，只要安装了jre，就可以运行Java程序）
- 自动内存管理
- 自动垃圾回收功能

现在的JVM不仅可以执行Java字节码文件，**还可以执行其他语言编译后的字节码文件**，是一个跨语言的平台。
## JVM的位置
**JVM是运行在操作系统之上的**，它与硬件没有直接的交互。
![在这里插入图片描述](https://img-blog.csdnimg.cn/84887e15e63743c4a2db48407032d07e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)![在这里插入图片描述](https://img-blog.csdnimg.cn/3cde699c7d3f4ad69b6fbf5081673f10.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)



- **JDK**：开发人员使用的工具包
	- **javac** ：用于编译Java源文件
	- **java**：用于运行Java程序，它会启动Java虚拟机，Java虚拟机加载相关的类，然后调用主程序main()方法
	- **jar**：将Java程序打包成一个`.jar`文件，
- **JRE**：运行Java程序所需要的环境
- **JVM**：将字节码解释编译为平台上的机器码指令

 ## JVM的组成
 1. 类加载器（ClassLoader）
 2. 运行时数据区（Runtime Data Area）
 3. 执行引擎（Execution Engine）
 4. 本地库接口（Native Interface）

 简图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f6c48b17baa443b99c44fadb8d6a4710.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

详细图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/1f51dabec80145bf985b3d96108ddf32.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
**组成部分的用途**
程序在执行之前要先把Java代码转换成字节码文件（.class文件），JVM首先需要把字节码通过一定的方式 **类加载器**（ClassLoader）把文件加载到内存中的**运行时数据区**（Runtime Data Area）,而字节码文件是JVM的一套指令集规范，并不能直接交给底层的操作系统去执行，需要特定的命令解析器 **执行引擎**（Execution Engine） 将字节码翻译成底层系统指令再交由CPU去执行，这个过程需要调用其他语言的接口 **本地库接口**（Native Interface）来实现整个程序的功能。

通常所说的JVM组成指 运行时数据区 ，通常调试分析的区域就是 **运行时数据区**，或者说是运行时数据区 中的**Heap** （堆）。

## Java代码的执行流程

![在这里插入图片描述](https://img-blog.csdnimg.cn/bd9733809cd245af937fe46a7252ef13.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/4cb225cb240249278922a9989c3cd586.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
Java编译器编译过程中，任何一个节点执行失败就会造成编译失败，虽然各个平台的Java虚拟机内部实现细节不尽相同，但是他们执行的字节码内容是一样的。

JVM主要任务就是负责将字节码装在到内部，解释 / 编译为对应平台上机器指令执行。JVM使用类加载器（Class Loader）装载class文件。

类加载完成后，会进行字节码校验，字节码校验通过之后，JVM解释器会把字节码翻译成机器码交由操作系统执行。但==不是所有的代码都是解释执行==，JVM对此作了优化，比如HotSpot虚拟机本身提供了JIT（Just In Time）编译器。

## JVM 架构模型
Java编译器输入的指令流基本上是一种基于**栈的指令集架构**
另一种指令集架构是基于**寄存器的指令集架构**。

**两种架构之间的区别**：	
- 基于栈式架构的特点：
	- 设计和实现更简单，适用于资源受限的系统
	- 使用零地址指令方式分配，其执行过程依赖于操作栈，指令集更小，编译器容易实现。
	- 不需要硬件支持，可移植型好，更好实现跨平台

- 基于寄存器式架构特点：
  - 指令完全依赖于硬件，可移植性差
  - 性能优秀，执行更高效
  - 完成一项操作使用的指令更少


执行 `2+3` 逻辑操作，指令如下：
1. 基于栈的计算流程（JVM为例）
	
```java
iconst_2   //常量2入栈
istore_1   
iconst_3   //常量3入栈
istore_2
iload_1    
iload_2
iadd       //常量2 3 出栈，执行相加
istore_0   //结果  5  出栈
```
2. 基于寄存器的计算流程
```java
mov eax,2  //将eax寄存器的值设为1
add eax,3  //将eax寄存器的值加3
```

使用javap -v class 可以将class文件反编译为指令集

所以由于跨平台的设计，Java指令集都是根据栈来设计的，不同CPU架构不同，因此不能设计为基于寄存器的。

- **优点**是跨平台，指令集小，编译器容易实现
- **缺点**是性能下降，实现同样功能需要更多的指令
