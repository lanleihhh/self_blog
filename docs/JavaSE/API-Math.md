# Math


Java为开发人员提供了数学计算的Math类,类中封装有许多静态方法,可以直接通过类名调用其中的方法

## 1.算术运算

- Math.max(int a, int b):返回最大值
- Math.min(int a, int b):返回最小值
- Math.abs(double a):返回绝对值
- Math.pow(a,b):返回a的b次方
- Math.sqrt(double a):返回平方根
- Math.cbrt(double a):返回立方根

```java
System.out.println(Math.PI);//圆周率
System.out.println(Math.max(5,8));
System.out.println(Math.min(5,8));
System.out.println(Math.abs(-2.7));//绝对值
System.out.println(Math.sqrt(100));//平方根
System.out.println(Math.cbrt(27));//立方根
System.out.println(Math.pow(2,3));//左的右次方,2的3次
```

运行结果:

```java
3.141592653589793
8
5
2.7
10.0
3.0
8.0
```

## 2.进位

- Math.round()四舍五入

- Math.rint()返回最接近参数的浮点值
- Math.floor()向下取整
- Math.ceil()向上取整

```java
/*
            Math.round() 四舍五入
            float返回int
            double返回long
         */
        System.out.println(Math.round(4.5));
        System.out.println(Math.round(-4.4));
        /*
            Math.rint()
            返回最接近参数的浮点值
         */
        System.out.println(Math.rint(4.6));
        System.out.println(Math.rint(-4.4));
		
		System.out.println("==================");

        /*
        Math.floor() 向下取整(返回值小于等于参数)
        Math.ceil()  向上取整(返回值大于等于参数)
         */
        System.out.println(Math.floor(9.99));//向下取整
        System.out.println(Math.floor(-3.19));
        System.out.println(Math.ceil(9.01));//向上取整
        System.out.println(Math.ceil(-3.01));
```

运行结果:

```java
5
-4
5.0
-4.0
==================
9.0
-4.0
10.0
-3.0
```

## 3.随机数

- Math.random()       产生大于等于0.0且小于1.0的随机数

```java 
System.out.println(Math.random());
```

运行结果:

0.050252970969030475
