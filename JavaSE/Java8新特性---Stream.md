
# Stream简介
Stream允许我们以声明式的方式==处理数据集合==,可以将Stream看作是遍历数据集合的==高级迭代器==。Stream 与Lambda表达式结合使用，将使编码效率大大提高，可读性增强。

>注意：**Stream**与IO中的**InputStream/OutputStream**是不同的概念

操作案例：![在这里插入图片描述](https://img-blog.csdnimg.cn/d82860f975d84cf786dcb287dc9dcaac.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)使用stream将集合中的手机按照序号num从小到大排序：
![在这里插入图片描述](https://img-blog.csdnimg.cn/29e1be1aa0bb472c836120b2d1b3e92d.png)
结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/03f9597484544655b0c71ac30fa2d517.png)
**Stream流的操作顺序**：

![在这里插入图片描述](https://img-blog.csdnimg.cn/3704a926c20b4eb8bfed6803192614fc.png)
**Stream语法**：
```java
| stream of elements +-----> |filter+-> |sorted+-> |map+-> |collect|
```

# Stream用法
## 获取流
1. **Collection接口下的stream()获取流**

![在这里插入图片描述](https://img-blog.csdnimg.cn/aef54da81f344ae9958d7671ca76d780.png)
```java
List<String> list = new ArrayList<>();
Stream<String> stream = list.stream();
```
2. **使用 **Arrays 中的 stream()** 方法，将数组转成流**

![在这里插入图片描述](https://img-blog.csdnimg.cn/cf9fa169190d4b58b34c43d97e8e1e92.png)
```java
Integer[] arr= new Integer[10]; 
Stream<Integer> stream = Arrays.stream(arr);
```
3. **使用 Stream 中的静态方法：of()**

![在这里插入图片描述](https://img-blog.csdnimg.cn/64dba321f36347f683c042c6bc1efcf5.png)

```java
Stream<Integer> stream = Stream.of(4,3,2,7,5,1,6);
```
4. **使用 BufferedReader.lines() 方法，将每行内容转成流**

![在这里插入图片描述](https://img-blog.csdnimg.cn/8301856f18ac46368203f2962ba6f6bd.png)

```java
BufferedReader reader=new BufferedReader(new FileReader("test.txt")); 
Stream<String> lineStream = reader.lines();
```
## 流操作
流操作可以分为==中间操作==和==终端操作==
```java
List<Apple> apples = 
			applestore .stream() 获得流
			 		   .filter(a -> a.getColor().equals("红色")) 中间操作
			 		   .collect(Collectors.toList()); 终端操作
```

简单来说Stream的流程就是
==数据源->中间操作->终端操作->结果==
### 中间操作
- **filter()**:过滤流中的某些元素
- **sorted()**:自然排序，流中元素需实现Comparable接口
- **distinct()**：去除重复元素
- **limit(n)**：获取n个元素
- **skip(n)**:跳过n个元素，配合limit(n)可实现分页
- **map()**:将其映射为一个新的元素
![在这里插入图片描述](https://img-blog.csdnimg.cn/f588f43c11de41f7a3a17f0e01a6b00c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/7c1023fc542c45d4acddc4d535e50d7d.png)
**map():**
1. 对集合中的每一个元素求平方
![在这里插入图片描述](https://img-blog.csdnimg.cn/398bd688f39c4ef6b48671cc19549f72.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/808f8da295d24bd5a2889fe00a98c161.png)
2.使用map()将String型的整数转为Integer型的整数
![在这里插入图片描述](https://img-blog.csdnimg.cn/45321950fa4341cc83d205ad6d20b4ab.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0fb915202a38483ca29443a8910a131a.png)




### 终端操作
- **forEach()**:遍历流中的元素
- **toArray()**:将流中的元素倒入一个数组中
- **Min()**：返回流中元素的最小值
- **Max()**：返回流中元素的最大值
- **count()**:返回流中元素的总个数
- **reduce()**:所有元素求和
- **anyMatch()**:接收一个Predicate函数，只要流中有一个元素满足条件则返回true，否则返回false
- **allMatch()**:接收一个Predicate函数，当流中所有元素都符合条件时才返回true，否则返回false
- **findFirst()**：返回流中的第一个元素
- **collect()**:将流中的元素倒入一个集合（Collection/Map）

```java
/**
         * 终端操作 -- 结果
         */
        Integer[] arr = new Integer[]{3,1,5,7,2,6,4,4,0};
        Object[] arr1 =  Arrays.stream(arr)
                .distinct()
                .sorted()
                .toArray();        //toArray()将流中元素倒入Object数组中
        //forEach()
        Arrays.stream(arr1).forEach(e-> System.out.print(e+" "));
        //count 统计
        Long count = Arrays.stream(arr).count();
        System.out.println(count);
        //anyMatch 流中任意一个元素符合条件时返回true
        boolean any = Arrays.stream(arr).anyMatch((e)->{return e>3;});
        System.out.println("有1个元素>3 ："+any);
        //anyMatch 流中所有元素符合条件时返回true
        boolean all = Arrays.stream(arr).allMatch((e)->{return e>3;});
        System.out.println("所有元素>3 ："+all);
        //findFirst 返回流中第一个元素
        System.out.println("流中第一个元素 :"+Arrays.stream(arr).findFirst());
        //reduce:所有元素求和
        Optional<Integer> res = Arrays.stream(arr).reduce((a, b)->a+b);
        System.out.println("元素求和 :"+res);
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/1719620b32d841c2856a000d8e02f1ab.png)
关于Optional类:https://blog.csdn.net/lanleihhh/article/details/123097262

