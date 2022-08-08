# Object类

## 一、Object类简介

Object类是java.lang包下的核心类（JDK 1.0开始），Object类是所有Java类的祖先（基类）。每个类都默认或显式使用 Object 作为 超类（父类）。所有对象（包括数组）都实现了这个类的方法。 

```java
public class A extends Object{}//显式继承Object类作为父类
public class A {}//默认继承Object类作为父类
```

```java
package Test.objectdemo;
//默认继承了Object类
public class ObjectDemo1 {
}
```

```java
package Test.objectdemo;
//显式继承了Object类
public class ObjectDemo2 extends Object{
    String name;

    //构造器
    public ObjectDemo2(String name) {
        this.name = name;
    }

    //获取name
    public String getName() {
        return name;
    }

    //设置name
    public void setName(String name) {
        this.name = name;
    }

    //重写了Object类的toString() 与 equals() 方法
    @Override
    public boolean equals(Object obj) {
        if(this==obj){//这里的this指代当前对象
            return true;
        }else if(obj==null||getClass()!=obj.getClass()){
            return false;
        }
        ObjectDemo2 obj2 = (ObjectDemo2) obj;//向下转型
        return this.name.equals(obj2.name);
    }

    @Override
    public String toString() {
        return "Objectdemo{"+"name："+name+"}";
    }
}

```

现在我们对“未继承”的ObjectDemo1和继承了Object的ObjectDemo2测试

```java
package Test.objectdemo;

public class ObjectTest {
    public static void main(String[] args) {
        ObjectDemo1 obj1 = new ObjectDemo1();
        ObjectDemo1 obj2 = new ObjectDemo1();
        ObjectDemo1 obj3=obj1;//两个引用指向一个对象

        System.out.println(obj1.toString());
        //未重写toString()和equals()方法，调用Object类的方法
        System.out.println(obj1.equals(obj2));//false
        System.out.println(obj1.equals(obj3));//true
        System.out.println("==========================");

        ObjectDemo2 obj4 = new ObjectDemo2("Tom");
        ObjectDemo2 obj5 = new ObjectDemo2("Tom");
        ObjectDemo2 obj6 = new ObjectDemo2("Danny");
		
        //重写了toString()和equals()方法，调用重写的方法
        //重写的equals()除了判断两对象地址相等，添加了判断对象属性的值相等
        System.out.println(obj4.toString());
        System.out.println(obj4.equals(obj5));//true
        System.out.println(obj4.equals(obj6));//false
    }
}
```

运行结果：

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-qArc8T5N-1620823636252)(API_img/p5.jpg)\]](https://img-blog.csdnimg.cn/20210512204756648.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 二、Object类的一些方法

```java
package Test.objectdemo;
import java.util.Arrays;

public class ObjectMethod {
    /*
        Object类的toString()
        public String toString() {
            return getClass().getName() + "@" + Integer.toHexString(hashCode());
        }

        Object类的equals()
        public boolean equals(Object obj) {
            return (this == obj);
        }
        */
    public static void main(String[] args) {
        Integer i1 = new Integer("100");
        Integer i2 = new Integer(100);

        /*
        Integer中重写的equals()方法
        public boolean equals(Object obj) {
            if (obj instanceof Integer) {
                return value == ((Integer)obj).intValue();
            }
            return false;
        }
        */
        System.out.println(i1.equals(i2));
        System.out.println(i2.toString());
        System.out.println("======================");
        int [] a = new int[]{1,2,3,4};
        int [] b = new int[]{1,2,3,4};
        int [] c = a;

        System.out.println(a.equals(b));
        System.out.println(a.equals(c));
        System.out.println(Arrays.toString(a));
        /*
        重写的toString()
        public static String toString(Object[] a) {
        if (a == null)
            return "null";
        int iMax = a.length - 1;
        if (iMax == -1)
            return "[]";
        StringBuilder b = new StringBuilder();
        b.append('[');
        for (int i = 0; ; i++) {
            b.append(String.valueOf(a[i]));
            if (i == iMax)
                return b.append(']').toString();
            b.append(", ");
        }
         */
    }
}

```

运行结果：
![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-qfLwgAIt-1620823636254)(API_img/p6.jpg)\]](https://img-blog.csdnimg.cn/20210512204809208.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)
几乎Java的API中所有类都重写了toString/equals等方法






