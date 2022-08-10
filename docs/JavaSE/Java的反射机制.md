# 反射机制
Java反射是在程序运行的过程中：
- 对任意的一个类，知道它的全类名，就可以获得这个类的属性或方法。
- 对任意的一个对象，都可以访问它的信息

==动态获取类信息、动态调用对象方法的功能就叫做Java的反射机制==


![在这里插入图片描述](https://img-blog.csdnimg.cn/d2f3214661144787ae7cd8e31477ab80.png)
<font color='red'>一个类的class文件被加载到内存中，会创建一个类的Class对象。
类的信息都会封装在Class对象里
一个类在内存中只有一个Class对象，所有该类的实例的class对象都是同一个<font>

```java
public class Test1 {
    public static void main(String[] args) throws ClassNotFoundException {
        //通过反射获取类的Class对象
        Class c1 = Class.forName("com.company.reflect.test.User");
        Class c2 = Class.forName("com.company.reflect.test.User");
        Class c3 = Class.forName("com.company.reflect.test.User");
        //一个类在内存中只有一个Class对象
        //一个类被加载后，类的信息都会封装在Class对象里
        System.out.println(c1 == c2);//true
        System.out.println(c2 == c3);//true
    }
}
```

# Class 类
- 在`java.lang`包下
- Constructor、Method、Field 都是在`java.lang.reflect`包下
![在这里插入图片描述](https://img-blog.csdnimg.cn/83de383e35dc42849c479365cd3797ff.png)
## 获取Class类的几种方式
 1. **类名.class** 
 2. **Class.forName("全类名")**
 3. **对象.getClass()**
 4. <font color='red'><font>**获取包装类的Class对象 ： 包装类.TYPE**
 5. <font color='red'><font>**获取父类Class ： 子类class对象.getSuperClass()**
```java
public class GetClass {
    public static void main(String[] args) throws ClassNotFoundException {
        //通过类名.class获取
        Class c1 = User.class;
        //通过forName获取
        Class c2 = Class.forName("com.company.reflect.test.User");
        //通过对象.getClass()获取
        Class c3 = new User().getClass();
        
        //获取的class对象都是同一个，因为一个类在jvm中只会创建一个class对象
        System.out.println(c1 == c2 && c2 == c3);//true
        
		//获取包装类型的class
        //public static final Class<Integer>  TYPE = (Class<Integer>) Class.getPrimitiveClass("int");
        Class c4 = Integer.TYPE;
        System.out.println(c4);

        //获取父类的class
        Class c5 = c1.getSuperclass();//   int
        System.out.println(c5);   //   class java.lang.Object
    }
}
```

## 什么类型可以获取Class对象 ？
1. 所有的类
2. 接口
3. 一维数组     <font color='red'>这里一维和二维数组分开说，是因为一维数组和二维数组不是同一类型<font>
4. 二维数组
5. 注解
6. 枚举
7. 基本类型包装类
8. void
9. Class
10. 基本数据类型 

```java
public class Test2 {
    public static void main(String[] args) {
        Class c1 = Object.class;     // 类
        Class c2 = Comparable.class; //接口
        Class c3 = char[].class;     //一维数组
        Class c4 = int[][].class;    //二维数组
        Class c5 = Override.class;   //注解
        Class c6 = Thread.State.class;//枚举类型
        Class c7 = Integer.class;     //基本类型包装类
        Class c8 = void.class;        //void
        Class c9 = Class.class;       //Class类的class对象
        Class c10 = double.class;       //基本类型

        System.out.println(c1);//class java.lang.Object
        System.out.println(c2);//interface java.lang.Comparable
        System.out.println(c3);//class [C
        System.out.println(c4);//class [[I
        System.out.println(c5);//interface java.lang.Override
        System.out.println(c6);//class java.lang.Thread$State
        System.out.println(c7);//class java.lang.Integer
        System.out.println(c8);//void
        System.out.println(c9);//class java.lang.Class
        System.out.println(c10);//double
    }
}
```
## 如何理解一个类型只有一个Class对象？
数组也是引用类型，求数组的Class对象，只要是同一类型，不管创建了几个数组，长度是否相同，它们使用的都是同一个class对象
==这里需要注意一维数组和二维数组的Class对象不同==
```java
public class Test {
    public static void main(String[] args) {
        int[] a = new int[10];
        int[] b = new int[100];
        int[][] c = new int[10][10];
		//一维数组a,b都是int类型，长度不同，它们的class对象依然只有一个
        System.out.println(a.getClass().hashCode());//460141958
        System.out.println(b.getClass().hashCode());//460141958
        System.out.println(c.getClass().hashCode());//1163157884
    }
}
```
## Integer.class 与 Integer.TYPE 的区别

```java
System.out.println(Integer.class);//class java.lang.Integer

System.out.println(int.class);    //int
System.out.println(Integer.TYPE); //int
```
- int.class是基本类型int的class对象
- Integer.TYPE是将int.class当做Integer的属性了
- **Integer.class才是Integer本身的class对象**
![在这里插入图片描述](https://img-blog.csdnimg.cn/ade8f2ce1f6a4986a443a7cda022d324.png)

# 反射实例化对象
对象.newInstance()
注意：==使用newInstance实例化对象，要求类必须有无参构造方法==
```java
public class User {
    private String name;
    private int age;

    public User() {}

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```
测试

```java
public static void main(String[] args) {
        Class userClass = User.class;
        User user = null;
        try {
        	//使用class对象.newInstance()实例化对象
            user = (User) userClass.newInstance();
        } catch (InstantiationException e) {
            throw new RuntimeException(e);
        } catch (IllegalAccessException e) {
            throw new RuntimeException(e);
        }
        System.out.println(user);//User{name='null', age=0}
    }
```
==去掉无参构造，使用newInstacne()会抛出 InstantiationException 异常==
![在这里插入图片描述](https://img-blog.csdnimg.cn/5e131c61a4e840d990f436120f264af8.png)


# Field 类
## 获取单个属性
```java
String getName()	      返回属性名
int getModifiers()	      获取属性的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号【一般配合Modifier类的toString(int x)方法使用】
Class<?> getType()	      以Class类型，返回属性类型【一般配合Class类的getSimpleName()方法使用】
void set(Object obj, Object value)	设置属性值
Object get(Object obj)	  读取属性值
```

```java
public static void main(String[] args) throws NoSuchFieldException, InstantiationException, IllegalAccessException {
		//反射拿到User的私有属性对象
        Field name = User.class.getDeclaredField("name");
        Field age = User.class.getDeclaredField("age");
		//实例化User
        User user = User.class.newInstance();
        //设置User的属性
        name.set(user,"张飞");
        age.set(user,20);
        System.out.println(user);
    }
```
程序抛出了`java.lang.IllegalAccessException`非法访问异常

```java
Class com.company.reflect.test.TestFiled can not access a member of class
com.company.reflect.test.User with modifiers "private"
不能访问类中private修饰的成员
```
已经拿到了私有的属性，还是不能访问，需要设置私有属性可以访问
![在这里插入图片描述](https://img-blog.csdnimg.cn/d0c833603f0348ddb890d3ec2fefbaed.png)
使用setAccessible方法设置可以被访问
![在这里插入图片描述](https://img-blog.csdnimg.cn/df709ce95a084bd4a629d31fba847fef.png)
## 获取一组属性
```java
public class TestFiled {
    public static void main(String[] args) throws NoSuchFieldException, InstantiationException, IllegalAccessException {
        User user = User.class.newInstance();
        Field[] fields = User.class.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            if(field.getName().equals("name")){
                field.set(user, "关羽");
            }
            if(field.getName().equals("age")){
                field.set(user, 40);
            }
        }
        System.out.println(user);//User{name='关羽', age=40}
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ad8ec21de4b44662b5a8963a9f7083eb.png)

# Constructor 类
Constructor 封装了类的构造方法

```java
String getName()	             返回构造方法名
int getModifiers()	             获取构造方法的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号
								【一般配合Modifier类的toString(int x)方法使用】
Class<?>[] getParameterTypes()	 返回构造方法的修饰符列表（一个方法的参数可能会有多个。）
								【结果集一般配合Class类的getSimpleName()方法使用】
T newInstance(Object … initargs) 创建对象【参数为创建对象的数据】
```

## 获取一个构造器
```java
public class TestConstructor {
    public static void main(String[] args) throws InstantiationException, IllegalAccessException, NoSuchMethodException, InvocationTargetException {
        //获取无参构造
        Constructor constructor = User.class.getConstructor();
        System.out.println(constructor);//public com.company.reflect.test.User()

        //获取有参构造
        Constructor constructor1 = User.class.getConstructor(String.class,int.class);
        System.out.println(constructor1);//public com.company.reflect.test.User(java.lang.String,int)

        //获取私有构造器
        Constructor constructor2 = User.class.getDeclaredConstructor(String.class);
        System.out.println(constructor2);//

        //使用反射获取的构造器来实例化对象
        User user1 = (User) constructor.newInstance();
        System.out.println(user1);//User{name='null', age=0}

        User user2 = (User) constructor1.newInstance("张飞",20);
        System.out.println(user2);//User{name='张飞', age=20}

        constructor2.setAccessible(true);//设置私有构造器可以被访问
        User user3 = (User) constructor2.newInstance("关羽");
        System.out.println(user3);//User{name='关羽', age=0}
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/175835a665a149e89b751421a272d064.png)
## 获取一组构造器
```java
public static void main(String[] args) {
        //获取User的所有构造器，含私有构造
        Constructor[] constructors = User.class.getDeclaredConstructors();
        for (Constructor constructor : constructors) {
            System.out.print("构造器名:"+constructor.getName());
            System.out.print("  构造器修饰符："+constructor.getModifiers());
            System.out.print("  参数数量："+constructor.getParameterCount());
            System.out.println("  构造器参数类型"+constructor.getParameterTypes());
        }
    }
```


# Method 类
|方法名|说明  |
|--|--|
|String getName()  | 返回方法名 |
|  int getModifiers() |获取方法的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号【一般配合Modifier类的toString(int x)方法使用】 	  |
|Class<?> getReturnType()  | 以Class类型，返回方法类型【一般配合Class类的getSimpleName()方法使用】 |
| Class<?> getReturnType() | 返回方法的修饰符列表（一个方法的参数可能会有多个。）【结果集一般配合Class类的getSimpleName()方法使用】 |
| Object invoke(Object obj, Object… args)() | 调用方法 |

## 获取单个方法
定义方法

```java
	void display(String name){
        this.name = name;
        System.out.println(this.toString());
    }

    void display(){
        System.out.println(this.toString());
    }
```

```java
public class TestMethod {
    public static void main(String[] args) throws NoSuchMethodException, InstantiationException, IllegalAccessException, InvocationTargetException {
        User user = User.class.newInstance();
        
        //无参的方法
        Method display1 = User.class.getDeclaredMethod("display");
        display1.invoke(user);//User{name='null', age=0}

        //根据方法名和class类型的参数列表，获取方法
        Method display2 = User.class.getDeclaredMethod("display", String.class);
        //实例化对象
        //执行方法:将对象与方法绑定，输入参数
        display2.invoke(user,"张飞");//User{name='张飞', age=0}
    }
}
```
## 获取一组方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/b0f805898d384e089069a0b6f7f42cea.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/e1ce2e0e5c6f49b5bc9ea5e35cbee278.png)



