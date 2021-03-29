# 常见问题

## 1.无法将位于xxx的资源添加到Web应用程序[/JXT3]的缓存中

11-Mar-2021 11:53:56.950 警告 [localhost-startStop-1] org.apache.catalina.webresources.Cache.getResource 无法将位于[/WEB-INF/classes/WEB-INF/classes/jxt/feedback/view/Feedback_Action_add1.jsp]的资源添加到Web应用程序[/JXT3]的缓存中，因为在清除过期缓存条目后可用空间仍不足 - 请考虑增加缓存的最大空间。



解决:在 /conf/context.xml 的 前添加以下内容

``` xml
<Resources cachingAllowed="true" cacheMaxSize="你所允许的最大缓存空间，单位是KB" />
```



## 2.Tomcat启动问题：严重[main] org.apache.catalina.core.AprLifecycleListener.init An incompatible version...

今天观察tomcat启动日志，有一些以前没注意到的信息：

严重 [main] org.apache.catalina.core.AprLifecycleListener.init An incompatible version [1.2.10] of the APR based Apache Tomcat Native library is installed, while Tomcat requires version [1.2.14]

虽然不影响使用，但还是别扭。原因是tomcat可移植运行库在C:\Windows\System32目录里缺少一个tcnative-1.dll文件。解决方法如下：
今天观察tomcat启动日志，有一些以前没注意到的信息：

解决：

``` 
打开tomcat官网：http://tomcat.apache.org/
进入下载页面 点击Tomcat Native 下载相应的环境
Native 1.2.26 Windows Binaries zip
之后将该文件复制到 C:\Windows\System32 下面即可，重启tomcat不再出现报错信息

https://blog.csdn.net/wn084/article/details/81227417
```

## 3.IDEA启动报错“tcnative-1.dll: Can‘t load AMD 64-bit .dll on a IA 32-bit platform”的解决办法







## 4. 

