

# File类

File类 主要用于文件和目录的创建、文件的查找和文件的删除等 

File对象代表磁盘中实际存在的目录或文件.通过构造方法创建一个File对象.

|      | 构造方法                             | 说明                                                         |
| ---- | ------------------------------------ | ------------------------------------------------------------ |
| 1    | `File(File parent, String child)`    | 通过给定的父抽象路径名和子路径名字符串创建一个新的File实例   |
| 2    | `File(String pathname) `             | 通过将给定路径名字符串转换成抽象路径名来创建一个新 File 实例 |
| 3    | `File(String parent, String child) ` | 根据父路径名字符串和子路径名字符串创建一个新 File 实例       |
| 4    | `File(URI uri) `                     | 通过将给定的 file: URI 转换成一个抽象路径名来创建一个新的 File 实例 |

- `File(String pathname) `

```java
//在当目录下创建了一个名为java.txt的文件-->相对路径(根据当前目录变化)
File file = new File("java.txt");
//在D盘中创建一个名为C#.txt的文件-->绝对路径
File file1 = new File("D:/C#.txt");
```

- `File(String parent, String child) `

```java
File file = new File("D:/", "java.txt");
```

- `File(File parent, String child)`

```java
File file = new File(new File("D:/"), "java.txt");
```

File对象可以对**文件或目录的属性**进行操作,如:**文件名**,**最后修改日期**,**文件大小**等

File对象不能**读/写**文件

File的常用方法

| 方法                                | 说明                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| **public String getName()**         | 返回该文件或目录的名称                                       |
| **public String getParent()**       | 返回父路径名字符串,没有返回null                              |
| **public File getParentFile()**     | 返回父路径名,没有返回null                                    |
| **public String getPath()**         | 返回路径                                                     |
| **public boolean isAbsolute()**     | 判断是否为绝对路径                                           |
| **public String getAbsolutePath()** | 返回绝对路径                                                 |
| **public File getAbsoluteFile()**   | 返回此抽象路径名的绝对形式。 相当于`new File(this.getAbsolutePath())` |
| **public boolean canRead()**        | 判断文件是否可读取                                           |
| **public boolean canWrite()**       | 判断文件是否可写入                                           |
| **public boolean exists()**         | 判断文件或目录是否存在                                       |
| **public boolean isFile()**         | 判断FIle对象是否为文件                                       |
| **public long lastModified()**      | 返回最后一次被修改的时间                                     |
| **public long length()**            | 返回文件的长度(字节数)                                       |
| **public boolean isHidden()**       | 判断文件是否为隐藏文件                                       |

测试

```java
		File file = new File("D:/java.txt");
        System.out.println(file.isFile());//
        String parent = "D:/";
        File file1 = new File(parent, "java.txt");
        File file2 = new File(new File("D:/"), "jj.txt");
        File file3 = new File("E:/lanlei/IdeaProjects/Java_Io/src/filedemo/java.txt");

        System.out.println("canRead: "+file.canRead());//判断文件是否可读
        System.out.println("canWrite: "+file.canWrite());//判断文件是否可写
        System.out.println("exists: "+file.exists());//判断文件是否存在
        System.out.println("absFile:"+file.getAbsoluteFile());//获取绝对路径
        System.out.println("absPath:"+file3.getAbsolutePath());//获取绝对路径
        System.out.println("parent:  "+file3.getParent());//获取父目录
        System.out.println("parentFile:  "+file3.getParentFile());//获取父目录
        System.out.println("name :"+file.getName());//获取文件名
        System.out.println("lenth : "+file.length());//长度
        System.out.println("isHidden : "+file.isHidden());//判断是否为隐藏文件
        System.out.println("isDirectory :"+file.isDirectory());//判断是否为目录
        System.out.println("lastModified:"+new Date(file.lastModified()));//最后修改时间
        System.out.println("path:"+file3.getPath());//获取路径
```

运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/f391b5456adb487ba10acc762665ef6d.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)




对目录/文件的操作方法

| 方法                                         | 说明                                        |
| -------------------------------------------- | ------------------------------------------- |
| `  public boolean delete()`                  | 删除文件或目录(目录必须为空)                |
| `public boolean isDirectory()`               | 判断File对象是否为目录                      |
| `public boolean createNewFile()`             | 当目标文件不存在时,创建新的同名空文件       |
| `public boolean mkdir()`                     | 创建单级目录                                |
| `public boolean mkdirs()`                    | 创建多级目录                                |
| `public String[] list()`                     | 返回该目录中文件名和目录名组成的字符串      |
| `public File[] listFiles()`                  | 获取该目录中所有文件名和目录名,返回文件对象 |
| `public File[] listFiles(FileFilter filter)` | 返回满足过滤器的File数组                    |

```java
package filedemo;
import java.io.File;
import java.io.FileFilter;
import java.io.IOException;
public class FileDemo2 {
    public static void main(String[] args) throws IOException {
        /*
        创建文件
         */
        File file = new File("E:/yun.txt");
        if(!file.exists()){
            try{
                file.createNewFile();//创建文件
            }catch (IOException e){
                e.printStackTrace();
            }
        }
        file.delete();//删除文件

        /*
        创建目录/文件夹
         */
        File file1 = new File("E:/Temp");
        File file2 = new File("E:/temp/temp1/temp2");
        file1.mkdir();//创建单级目录
        file2.mkdirs();//创建多级目录

        file2.delete();//删除文件夹时,一次只能删除一级且文件夹为空

        /*
        删除文件夹
         */
        File file3 = new File("E:/百度云/智慧树");
        //获取当前目录下的所有文件名和目录名,返回字符串
        String [] fileName = file3.list();
        for(String str : fileName){
            System.out.println(str);
        }

        //获取当前目录下的所有文件名和目录名,返回文件对象
        File [] files = file3.listFiles();
        for(File f : files){
            f.delete();
        }

        /*
        自定义文件过滤
         */
        File file4 = new File("E:/百度云");
        File [] files1 = file4.listFiles(new FileFilter(){

            @Override
            public boolean accept(File pathname) {
                return pathname.getName().endsWith("pdf");//返回后缀名为pdf的文件名
            }
        });

        for (File f:files1){
            System.out.println(f);
        }
    }
}
```


运行结果:

![在这里插入图片描述](https://img-blog.csdnimg.cn/98a9a3a1255b4b5cbb1b2cefabd9b3de.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhbmxlaWhoaA==,size_16,color_FFFFFF,t_70#pic_center)





