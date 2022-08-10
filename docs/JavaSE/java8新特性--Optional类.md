# java8新特性--Optional类

> Optional类可以解决空指针问题

以前处理空指针

```java
	/**
     * 以前处理空指针
     */
    @Test
    public void test01(){
        //String name = "张三";
        String name = null;
        if(name!=null){
            System.out.println(name.length());
        }else{
            System.out.println("is null");
        }
    }
```

Optional类(自1.8之后)是一个**没有子类**的工具类,是一个**值可以为Null**的容器,主要作用是**防止NullPointerException**

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-75VzLilM-1645615215062)(C:/Users/lanlei/AppData/Roaming/Typora/typora-user-images/1645608291225.png)]

Optional的常用方法:

**创建Optional实例**

- **Optional.of(T t)** : 创建一个 Optional的实例对象,  ==of()不支持null==
- **Optional.empty()**: 创建一个空的Optional实例
- **Optional.ofNullable(T t)** : t为null创建空的Optional实例,不为null创建Optional实例

```java
/**
     * 创建Optional对象
     */
    @Test
    public void test02(){
        //of(T t) 不支持null
        Optional<String> op1 = Optional.of("hello");//Optional[hello]
        //Optional<String> op2 = Optional.of(null);//NullPointerException

        //empty) 创建一个空的实例
        Optional<String> op3 = Optional.empty();//Optional.empty

        //ofNullable(T t) 支持null
        Optional<String> op4 = Optional.ofNullable("hello");//Optional[hello]
        Optional<String> op5 = Optional.ofNullable(null);//Optional.empty
    }
```

**其他常用方法**

- **get()** : 如果Optional有值就返回,否则抛出`NoSuchElementException`异常

- **isPresent()** : 判断对象是否包含值,包含返回true,否则返回false
- **orElse(T t)** : 如果对象包含值,返回该值,否则返回t
- **orElseGet(Supplier other)** : 如果对象包含值,返回改值,否则返回Lambda表达式的返回值
- **ifPresent()** :  如果值存在则使用该值调用 consumer , 否则不做任何事情。 

```java
/**
     * 常用方法
     * get():如果Optional有值就返回,否则抛出`NoSuchElementException`异常
     * isPresent(): 判断对象是否包含值,包含返回true,否则返回false
     * orElse(T t) : 如果对象包含值,返回该值,否则返回t
     * orElseGet(Supplier other) : 如果对象包含值,返回改值,否则返回Lambda表达式的返回值
     */
    @Test
    public void test03(){
        Optional<String> op1 = Optional.of("hello");
        Optional<String> op2 = Optional.empty();

        if(op1.isPresent()){
            System.out.println(op1.get());//hello
        }
        //System.out.println(op2.get());//抛出 NoSuchElementException 异常

        String s1 = op1.orElse("张三");
        System.out.println(s1);//hello  -->对象包含值,返回包含的值
        String s2 = op2.orElse("李四");
        System.out.println(s2);//李四   --> 对象不包含值,返回参数值

        String s3 = op2.orElseGet(() -> {
            return "hello";
        });
        System.out.println(s3);//hello -->对象不包含值,返回lambda表达式中的返回值
    }
```

```java
	@Test
    public void test04(){
        Optional<String> op1 = Optional.of("hello");
        Optional<String> op2 = Optional.empty();

        //如果存在值,就执行...操作
        //op1.ifPresent(s-> System.out.println(s));
        op1.ifPresent(System.out::println);//hello
        op2.ifPresent(System.out::println);//没有值,不执行打印操作
    }
```



