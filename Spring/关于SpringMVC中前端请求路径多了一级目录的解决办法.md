# 关于SpringMVC中前端请求路径多了一级目录的解决办法

前端请求

```javascript
$.ajaxFileUpload({
    url: 'admin/fileUpload', //用于文件上传的服务器端请求地址  调用后端接口(文件上传的方法)
    fileElementId: 'fileId', //文件上传域的ID
    dataType: 'json', //返回值类型 一般设置为json
    success: function (data){//success:回调函数 ==$.post("",function(res){})
            })
```

对应后端接口(处理的方法,非interface)

```java
@Controller//控制层注解标签
@RequestMapping(path = "/admin")//请求映射
public class AdminController {

    //前端请求路径admin/fileUpload(对应后端: 类/方法)
    @PostMapping(path = "/fileUpload")
    public CommonResult upload(@RequestParam("fileName") CommonsMultipartFile file, HttpSession session){
    	//
    }

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b1dda8638e7241268db3b1fba355d487.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/6125da4d587443a4b644df98a57b6308.png#pic_center)


报了404,看到请求地址莫名多了一级目录,发现是**没有添加`@ResponseBody`注解标签**

**@ResponseBody**:

将 controller 的方法返回的对象通过适当的转 

换器转换为指定的格式之后，写入到 response 对象的 body 区，通常用来向异 

步请求返回 JSON 数据。 

注意：在使用此注解之后不会再走视图处理器，而是直接将数据写入到输入流中， 



注意：在使用此注解之后不会再走视图处理器，而是直接将数据写入到输入流中， 

他的效果等同于通过 response 对象输出指定格式的数据
