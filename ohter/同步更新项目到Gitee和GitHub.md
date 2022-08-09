# 同步更新项目到Gitee和GitHub

## 1.clone Gitee项目，添加GitHub的仓库地址 

修改：项目/.git/config

![image-20220809091253719](https://s2.loli.net/2022/08/09/eo3hizXVqURd84L.png)

![image-20220809091323415](https://s2.loli.net/2022/08/09/Ymk6oNKAu59UHBS.png)

## 2.TLS certificate verification has been disabled! TLS证书验证已禁用！

![image-20220809091531320](https://s2.loli.net/2022/08/09/PrcCe712gFupyEk.png)

执行这条命令

```xml
git config --global http.sslVerify true
```



## 3.Please make sure you have the correct access rights and the repository exist

请确保您具有正确的访问权限并且存储库存在

![image-20220809091801274](https://s2.loli.net/2022/08/09/aZg72Xjfu8BDomw.png)

1. C盘寻找`.ssh`文件夹，记事本打开`id_xxx.pub`文件，复制
2. github,进入settings->SSH and GPG keys->New SSH Key,粘贴







