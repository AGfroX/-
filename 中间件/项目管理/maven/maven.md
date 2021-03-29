

##  本地jar包安装

```xml
1.安装命令:
mvn install:install-file -DgroupId=hdz -DartifactId=hdz-framework -Dversion=2020.01.01 -Dpackaging=jar -Dfile=F:/maven-xxx/hdz-framework-2020.01.01.jar

2.本地引用
建好本地目录
<dependency>
    <groupId>jxt</groupId>
    <artifactId>jxt-web-template</artifactId>
    <version>1.0</version>
    <systemPath>F:/maven/repository/jxt/jxt-web-template/1.0/jxt-web-template-1.0.jar</systemPath>
</dependency>


```



## maven 项目无法识别、 maven 工具View 无法打开

``` txt
右击pom文件，add as maven project
```

