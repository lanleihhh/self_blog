# 递归

普通方法:

![在这里插入图片描述](https://img-blog.csdnimg.cn/f319b552ff534306bcdd2fec3a9e2bb4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


递归方法:

![在这里插入图片描述](https://img-blog.csdnimg.cn/2f69867db535451bb2a05adeef34df25.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


**递归**:递归将**原本的问题**转化为**更小的同一问题**(也就是函数/方法**调用自身**)

**使用递归**:代码中**先写递归到底**(递归结束条件)的情况,**再写递归调用**

先来看一个求累加的问题:1+2+3+...+100

最开始的时候我们使用循环去做,从1一直加到100或从100一直加到1

```java
package com.company.project.homework;

public class homework2 {
    public static void main(String[] args) {
        System.out.println(method15);//15
    }
    
    public static int method1(int x){
        int sum = 0;
        for (int i = 1; i <= x; i++) {
            sum+=i;
        }
        return sum;
    }
}
```

时间复杂度为O(n)

使用递归

```java
package com.company.project.homework;

public class homework2 {
    public static void main(String[] args) {
        System.out.println(method(5));//15
    }

    public static int method(int x){
        if(x == 1){//递归到底的情况
            return 1;
        }

        return x+method(x-1);//将原来问题转化为更小的同一个问题
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/403cd60b4cb546d3ab6edff745726973.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


**递归中终止条件的重要性**

递归递归,有递必须有归,只递不归会导致栈内存中可分配的空间越来越少,不足以为函数/方法分配空间,从而发生**栈溢出错误**

![在这里插入图片描述](https://img-blog.csdnimg.cn/682efdf55c364843a039ed091b8d4098.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


其他几个递归例子

- 斐波那契(不死神兔)

  假设一对**刚出生**的小兔**一个月后**就能**长成大兔**，再过一个月就能生下一对小兔，并且此后每个月都生一对小兔，一年内没有发生死亡，问：一对刚出生的兔子，一年内繁殖成多少对兔子? 

  ```java
  package com.company.project.homework;
  
  import java.util.ArrayList;
  
  /**
   * 不死神兔--斐波那契
   */
  public class homework1 {
      public static void main(String[] args) {
          System.out.println(test(6));
      }
  
  	//参数:月数 返回:n个月后兔子总数
      public static int test(int month) {
          if (month == 1 || month == 2) {
              return 1;
          } else {
              return test(month - 1) + test(month - 2);
          }
      }
  }
  ```

- 计算某个数的阶乘 (5!=`5*4*3*2*1`)

  ```java
  package com.company.project.homework;
  
  import java.math.BigInteger;
  
  /**
   * 阶乘
   * n*(n-1)*(n-2)*....*1
   */
  public class homework3 {
      public static void main(String[] args) {
          System.out.println(method(BigInteger.valueOf(32)));
          //输出:263130836933693530167218012160000000
      }
  
      //BigInteger  大数运算
      private static BigInteger method(BigInteger b) {
          if(b.equals(BigInteger.ONE)){//BigInteger.ONE:1
              return BigInteger.ONE;
          }else{//multiply:乘  subtract:减
              return b.multiply(methodBig(b.subtract(BigInteger.ONE)));
          }
      }
  }
  ```

  为什么这里用BigInteger类型作为参数?我们使用integer试一试

  ```java
  package com.company.project.homework;
  
  import java.math.BigInteger;
  
  /**
   * 阶乘
   * n*(n-1)*(n-2)*....*1
   */
  public class homework3 {
      public static void main(String[] args) {
          System.out.println(method(32));//输出:-2147483648
      }
      //基本数据类型 int -2147483648~2147483647
      private static Integer  method(Integer i) {
          if(i==1){
              return 1;
          }else {
              return i * methodInt(i-1);
          }
      }
  }
  ```

  - Integer类取值和 int 类型取值一致，取值范围是从-2147483648 至 2147483647 

  - 计算阶乘运算结果已经上限,Integer不能再接收返回的值了

-  一只小猴子一天摘了许多桃子，第一天吃了一半，然后忍不住又吃了一个；第二天又吃了一半，再加上一个；后面每天都是这样吃。到第10天的时候，小猴子发现只有一个桃子了。问小猴子第一天共摘了多少个桃子。 

  ```java
  package com.company.project.homework;
  
  /**
   * 猴子吃桃
   * 总计total个桃
   * 每天吃剩余的(1/2)+1个
   * 第十天剩 1 个桃  求total
   */
  public class homework4 {
      public static void main(String[] args) {
          System.out.println(method(10,1));//1354
          System.out.println(method(10));//1354
      }
  
      /**
       * @param day 第n天
       * @param res 剩余桃子
       * @return 第一天桃子数
       */
      private static int method(int day,int res) {
          if(day==1){
              return res;
          }
          else{
              day--;
              res = (res+1)*2;
              return method(day, res);
          }
      }
  
      /**
       *
       * @param day 倒数第n天
       * @return 第一天的数量
       */
      public static int method(int day){
          if(day == 1){
              return 1;
          }
          return (method(day-1)+1)*2;
      }
  }
  ```

- 杨辉三角

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/b5d2b6147e0b4284b9ef783f5ec92f60.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


  ​	

  ```java
  package com.company.project.homework;
  
  import java.util.Arrays;
  
  public class homework5 {
      public static void main(String[] args) {
  
          for (int i = 1; i <= 10; i++) {
              System.out.println(Arrays.toString(method(i)));
          }
      }
  
      private static int[] method(int i) {
          int[] arr = new int[i];
          if(i==1){
              arr[0] =1;
              return arr;
          }else{
              arr[0] = arr[i-1] = 1;//每行第一个与最后一个为1
              for (int j = 1; j < i-1; j++) {
                  //中间的数=上一行两数之和
                  arr[j] = method(i-1)[j-1] + method(i-1)[j];
              }
              return arr;
          }
      }
  }
  ```

![在这里插入图片描述](https://img-blog.csdnimg.cn/abd248e1976e4517a922172279f51b2f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

