# API---Arrays类

## 1 概述

 Arrays 类是一个数组工具类，在java.util包中,需要使用Arrays类的方法时,导入Arrays类:

**import java.util.Arrays**;

其中包含了数组操作的很多方法。这个 Arrays 类里均为 static 修饰的方法（static 修饰的方法可以直接通过类名调用），可以直接通过 Arrays.xxx(xxx) 的形式调用方法。 

## 2 示例

### 2.1Arrays.equals()

- **Arrays.equals()**

比较不同两数组内容是否相同

- **equals()**

比较两数组在内存中地址是否相等,即判断另一数组是否是其本身

```java 
//两方法源码

		//Arrays.equals()
        public static boolean equals(int[] a, int[] a2) {
            if (a==a2)
                return true;
            if (a==null || a2==null)
                return false;
            int length = a.length;
            if (a2.length != length)
                return false;
            for (int i=0; i<length; i++)
                if (a[i] != a2[i])
                    return false;
            return true;


		//equals()
        public boolean equals(Object obj) {
            return (this == obj);
        }

```

```java
		int [] a={1,2,3,4,5};
        int [] b={5,4,3,2,1};
        int [] c={1,2,3,4,5};

        System.out.println(a==c);//比较数组a,c在内存中的地址
        System.out.println(Arrays.equals(a,c));//判断两数组内容是否一致
        System.out.println(a.equals(c));//判断两者是否是同一对象
	
		/*
		输出:
		false
        true
        false
		*/
```

### 2.2Arrays.sort()

- **public static void sort(int[] a)** 

对整个数组排序(升序)

- **public static void sort(int[] a, int fromIndex, int toIndex)**

对数组指定区间排序(升序)

a:要排序的数组

fromIndex:排序开始索引(包括)

toIndex:排序结束索引(不包括,toIndex-1)

```java
		int [] a={5,4,3,2,1};

        /*
        全部排序
         */
        Arrays.sort(a);
        System.out.println(Arrays.toString(a));
        /*
        输出数组时,默认使用toString()输出地址
         */
        System.out.println(a.toString());//[I@1b6d3586
        System.out.println(a);//a.toString  [I@1b6d3586

		/*
		[1, 2, 3, 4, 5]
        [I@1b6d3586
        [I@1b6d3586 
		*/
```

```java
 		int [] a={5,4,3,2,1};
		/*
        局部排序
         */
        Arrays.sort(a,0,3);//第0索引~第3-1索引
        //将数组转为字符串输出
        System.out.println(Arrays.toString(a));

		/*
		输出:
		[3, 4, 5, 2, 1]
		*/
```

```java
		String [] str={"d","c","b","a","e"};
        //Arrays.sort(str);//排序
        Arrays.sort(str,0,3);//0~3-1  -->  0~2    将指定区间的元素排序
        System.out.println(Arrays.toString(str));
		
		/*
		输出:
		[b, c, d, a, e]
		*/
```

#### 2.2.1 自定义对象排序

自定义类,实现Comparable接口,重写其compareTo()方法

```java

public class Student implements Comparable<Student> {//实现Comparale接口
    private String name;
    private int num;

    public Student(int num,String name) {
        this.num = num;
        this.name = name;
    }

    //用来作比较,提供一个自定义排序规则的方法
    @Override//compareTo()方法会在sort()的底层调用
    public int compareTo(Student o) {
        //return this.num-o.num;//大于0 等于0 小于0 三种返回值
        return this.name.compareTo(o.name);
    }

    @Override
    public String toString() {
        return "Student{" +
                "num=" + num +
                ", name='" + name +
                '\'' +
                '}';
    }
}
```

```java
 public static void main(String[] args) {
        Student [] std=new Student[5];

       	Student s1=new Student(101,"ABC");
        Student s2=new Student(102,"AB");
        Student s3=new Student(103,"A");
        Student s4=new Student(104,"ABCDE");
        Student s5=new Student(105,"ABCD");

        std[0]=s5;
        std[1]=s4;
        std[2]=s3;
        std[3]=s2;
        std[4]=s1;

        //对引用数据类型-->数组进行排序
        Arrays.sort(std);
        System.out.println(Arrays.toString(std));

    }

	/*
	输出:
	[Student{num=103, name='A'}, 
	Student{num=102, name='AB'}, 
	Student{num=101, name='ABC'}, 
	Student{num=105, name='ABCD'}, 
	Student{num=104, name='ABCDE'}]

	*/
```

### 2.3Arrays.BinarySearch()

二分查找：查询指定指在数组中的位置

前提：    数组是有序的

```java
		int [] a={6,5,2,3,25,33,3,64,1,8,0,10};

        Arrays.sort(a);//先排序,再查找

        System.out.println(Arrays.binarySearch(a,7));//-8
        System.out.println(Arrays.binarySearch(a,4,8,3));//从a[4]~a[8]找3
        System.out.println(binarySearch(a,3));
        
        /*
        输出:
        -8
        4
        3
        */

		/*方法源码:
		public static int binarySearch(int[] a, int key) {
        int low = 0;//开始索引
        int high = a.length- 1;//结束索引

        while (low <= high) {//
            int mid = (low + high) >>> 1;
            //找中间位置    右移一位==除2       0100 4 >>>  0010 2
            int midVal = a[mid];//得到的中间值

            if (midVal < key)//若中间值<寻找值
                low = mid + 1;
            else if (midVal > key)//中间值>寻找值
                high = mid - 1;
            else
                return mid; // key found
        }
        return -1;  // key not found.
   		 }
		*/
```

### 2.4Arrays.copyOf()

- **Arrays.copyOf()**

返回一个指定长度的新数组,并将原数组中的内容复制到新数组中

```java
		int [] a={1,4,2,6,3,9,6,5,0};

        /*
        public static int[] copyOf(int[] original, int newLength) {
            int[] copy = new int[newLength];
            System.arraycopy(original, 0, copy, 0,
                             Math.min(original.length, newLength));
            return copy;
        }
        */

        //返回一个指定长度的新数组,并将原数组中的内容复制到新数组中
        System.out.println(Arrays.toString(Arrays.copyOf(a,15)));//复制数组到新数组中

        System.out.println(Arrays.toString(Arrays.copyOf(a,5)));//复制数组到新数组中

		/*
		输出	
		[1, 4, 2, 6, 3, 9, 6, 5, 0, 0, 0, 0, 0, 0, 0]
		[1, 4, 2, 6, 3]
		*/
```

## 一、冒泡排序

### 1.1算法思想：

在未排序的一组数中,重复访问未排序的元素,从第一个未排序元素开始,比较相邻两个元素大小,若上一元素大于(小于)下一元素,对调两元素位置,直至每一趟不能再交换(即排序完成)。

### 1.2算法原理:

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。

2. 对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。

3. 针对所有的元素重复以上的步骤，除了最后一个。

4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210417225810523.jpg#pic_center)


### 1.3代码实现：

```java
package Test.arraysort;
import java.util.Arrays;
public class bubbleSort {
    public static void main(String[] args) {
        
        /*冒泡排序
            2 0 1 5 3 8
        一: 0 1 2 5 3 8
        二  0 1 2 3 5 8
         */
		System.out.println("未排序:"+Arrays.toString(a));
        int [] a={2,0,1,5,3,8};//声明初始化数组
        for(int i=0;i<a.length-1;i++){//从第0个元素开始到第length-1个为止,相邻两个元素依次比较
            for(int j=0;j<a.length-1-i;j++){//外层每循环1次,下一次交换次数少比上次1次
                if(a[j]>a[j+1]){//前面元素比后面大,交换两元素
                    int temp=a[j];
                    a[j]=a[j+1];
                    a[j+1]=temp;
                }
            }
        }
        System.out.println("排序后:"+Arrays.toString(a));
    }
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-nKfhnPKP-1618671336437)(img/p3.jpg)\]](https://img-blog.csdnimg.cn/20210417225820805.jpg#pic_center)




## 二、选择排序(直接选择)

### 2.1算法思想:

从未排序第一个元素开始到最后一个中,选出最小(大)的元素,将其与第一个未排序元素交换,交换后第一元素变为已排序元素,第二元素为未排序第一个元素,继续重复这个步骤,直至排序完成

### 2.2算法原理

1. 从a[0]开始遍历至[a.length-1],找到最大元素,将它与a[0]交换,下次起始位置+1

2. 从a[1]开始遍历至[a.length-1],找打最大元素,将它与a[1]交换,下次起始位置+1
3. 重复这个步骤,直至最后两个数据完成交换,排序完成

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-O8C7jGDq-1618671336440)(img/p7.jpg)\]](https://img-blog.csdnimg.cn/20210417225831302.jpg#pic_center)


### 2.3代码实现

```java
package Test.arraysort;
import java.util.Arrays;
public class selectSort {
    public static void main(String[] args) {
        int[] a={3,1,5,0,6};
        System.out.println("未排序:"+Arrays.toString(a));
        for (int i = 0; i < a.length-1; i++) {//从第i个下标开始遍历
            for (int j = i+1; j < a.length; j++) {
                //从第i+1个下标开始遍历,i+1最大为a.length,访问数组会越界,将i设为小于a.length-1
                if(a[j]<a[i]){
                   change(a,i,j);//若未排序的大于已排序的末元素,交换两值
                }
            }
        }
        System.out.println("已排序:"+Arrays.toString(a));
    }
    public static void change(int[] arr,int a,int b){
        int temp=arr[a];
        arr[a]=arr[b];
        arr[b]=temp;
    }
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-7fRhooPH-1618671336445)(img/p4.jpg)\]](https://img-blog.csdnimg.cn/20210417225849207.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


上面的代码虽然实现了排序功能,但当未排序元素中,小于已排序末元素的有多个时,会交换多次,执行多行代码

我们可以将其改一改,使其少执行几行代码

```java
package Test.arraysort;
import java.util.Arrays;
public class selectSort {
    public static void main(String[] args) {
        int[] a={3,1,5,0,6};
        System.out.println("未排序:"+Arrays.toString(a));
        for (int i = 0; i < a.length-1; i++) {//已排序元素
            int minIndex=i;//将最小值索引设为i,i为已排序末元素
            for (int j = i+1; j <a.length; j++) {//未排序元素
                if(a[j]<a[i]){
                    minIndex=j;//若未排序小于已排序末元素,将其索引赋给最小值索引
                }
            }
            //当每次找到最小元素后再交换,相比于遇到一个小于已排序末元素的就交换,减少了执行代码次数
            int t=a[i];
            a[i]=a[minIndex];
            a[minIndex]=t;     //在未排序元素中找到最小元素后,将它与已排序末元素交换
        }
        System.out.println("已排序:"+Arrays.toString(a));
    }
}
```

运行结果:

![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-f8skP5S6-1618671336448)(img/p5.jpg)\]](https://img-blog.csdnimg.cn/20210417225904616.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)


## 三、插入排序

### 3.1算法思想

在未排序元素中,将元素在已排序元素中找到合适位置,依次插入到已排序元素中,使得每次插入后,已排序元素都是有序的,重复这样的步骤,直到排序完毕

### 3.2算法原理

1. 将第一个元素设为已排序的元素,从未排序的最后一个元素(待排元素)往前遍历
2. 若待排元素与前面的元素依次比较,





![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-zXb4htug-1618671336450)(img/p8.jpg)\]](https://img-blog.csdnimg.cn/20210417225924165.jpg#pic_center)


### 3.3代码实现

```java
package JavaSE.JavaArray.Sort;

import java.util.Arrays;

/*
        插入排序
        从第二个位置开始向前比较,到第0个,完成一次循环
        从后往前,将后面的数与前面所有数比较,若前面大,记录,一趟完成后,插入
 */
public class insertSort {
    public static void main(String[] args) {
        int[] a = {5, 4, 2, 1, 3};
        int current=0;//当前要排序的元素
        for (int i = 0; i < a.length-1; i++) {
            current=a[i+1];         //a[i+1]max=a[length],外循环需length-1
            int preIndex=i;         //当前所比较的位置
            for(;preIndex>=0&&current<a[preIndex];preIndex--){
                //当preIndex==0时,preIndex--==-1 结束循环
                a[preIndex+1]=a[preIndex];
            }
            a[preIndex+1]=current;
        }
        System.out.println(Arrays.toString(a));
    }
}
```
运行结果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210417230323576.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)