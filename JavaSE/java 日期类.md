#  Date/Calendar/ SimpleDateFormat 

## 1. Date 日期类

 表示特定的瞬间，精确到毫秒 

- 毫秒值表示1970年1月1日00：00：00.000 GMT之后的毫秒数

构造方法

```java
//系统当前时间的对象
Date d = new Date(); 
//给定毫秒值的对象
Date d = new Date(long d); 
```

示例

```java
import java.util.Date;
/*
日期类
 */
public class DateDemo {
    public static void main(String[] args) {
        /*
        Date date = new Date();//日期类,   包含程序运行那一刻的时间

        System.out.println(date.getTime());//1622329001845
        System.out.println(date);//Sun May 30 06:55:57 CST 2021  默认调用toString()
        System.out.println(date.toString());//Sun May 30 06:55:57 CST 2021
        System.out.println(date.toLocaleString());//2021-5-30 6:56:23   根据当地习惯显示时间

        */
        
        /*
        //诸如getYear()这种带删除线的方法,已经被官方弃用(有新方法代替),可以用但不推荐
        System.out.print(date.getYear()+1900);//源代码-1900
        System.out.print("-"+(date.getMonth()+1)); //0~11
        System.out.print("-"+date.getDate());//一月中的第几天
        System.out.print(" "+date.getHours());
        System.out.print(":"+date.getMinutes());
        System.out.print(":"+date.getSeconds());
        System.out.print(" 星期:"+date.getDay());//一周中第几天 0~6          2021-5-30 7:10:2 星期:0
        */

        Date date = new Date(9111111111111111111L);
        System.out.println(date);
        System.out.println(date.toLocaleString());//288721587-7-5 10:51:51
        date.setMonth(10);
        System.out.println(date.getMonth());
        System.out.println(date.toLocaleString());
        //288721587-11-9 10:51:51            源代码:d.setMonth(month + 1)
        
    }
}
```

## 2. Clalendar 日历类

 Java Calendar 是时间操作类,Calendar 抽象类定义了足够的方法，在某一特定的瞬间或日历上，提供年、月、日、小时之间的转换提供方法。  Calendar类是一个抽象类，在实际使用时实现特定的子类的对象，创建 对象的过程对程序员来说是透明的，只需要使用getInstance方法创建 即可。 

```java
import java.util.Calendar;
import java.util.GregorianCalendar;
/*
日历类
 */
public class CalendarDemo {
    public static void main(String[] args) {
        /*
        public abstract class Calendar implements Serializable, Cloneable, Comparable<Calendar>
         抽象类,不能实例化对象,可以实例化子类对象()
         */

        Calendar celandar = Calendar.getInstance();//返回子类对象
        /*Calendar gregorianCalendar = new GregorianCalendar();//向上转型

        System.out.println(celandar.getTime());         //Sun May 30 07:51:47 CST 2021
        System.out.println(gregorianCalendar.getTime());//Sun May 30 07:51:47 CST 2021
        System.out.println(celandar.get(Calendar.YEAR));            //2021
        System.out.println(gregorianCalendar.get(Calendar.MONTH)+1);//5
        System.out.println(celandar.get(Calendar.DAY_OF_MONTH));//  一月中的第n天
        System.out.println(celandar.get(Calendar.DAY_OF_YEAR));//   一年中的第n天
        System.out.println(celandar.get(Calendar.DAY_OF_WEEK));//   一周中的第n天
        System.out.println(celandar.get(Calendar.WEEK_OF_YEAR));//  一年中的第n周
        System.out.println(celandar.get(Calendar.WEEK_OF_MONTH));//   一月中的第n周*/

        celandar.set(2020,1,1);
        System.out.println(celandar.getTime());
        
    }
}
```

注意:

 Calendar 的 month 从 0 开始，月份 :0~11 

## 3. 日期格式化类

SimpleDateFormat( "yyyy-MM-dd" )   构造格式化日期的格式

format(Date date)     将指定的日期对象格式化为指定格式的字符串

```java
package api.day3;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

/*
    SimpleDateFormat
    日期格式化类

    构造方法
    SimpleDateFormat(year-month-day);

    日期转字符串
    Date now = new Date();
    myfmt.format(now);

    字符串转日期
    str = "2021-06-10";
    new SimpleDateFormat("str")
    
 */
public class SimpleDateFormatDemo {
    public static void main(String[] args) {
        /*
        Date d = new Date();
        System.out.println(d);//Thu Jun 03 19:03:56 CST 2021
        System.out.println(d.toLocaleString());//2021-6-3 19:04:33

        //日期格式化--把java中的对象型(new Date()  Calendar ) 转为指定格式的字符
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss E"  );
        //                                                               2021-06-03 19:14:58 星期四
        System.out.println( simpleDateFormat.format(d));
        //public final String format(Date date)*/
        
        //字符串转为日期对象
        String btd = "1999-12-06";
        SimpleDateFormat simpleDateFormat1 = new SimpleDateFormat("yyyy-MM-dd");
        try{
            Date date = simpleDateFormat1.parse(btd );
            System.out.println(date.getYear()+1900);//return normalize().getYear() - 190
            System.out.println(date.getTime());
        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
}
```

