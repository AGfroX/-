# maven原理



# maven优缺点

**优点如下：**

1. 简化了项目依赖管理：
2. 易于上手，对于新手可能一个"mvn clean package"命令就可能满足他的工作
3. 便于与持续集成工具（jenkins）整合
4. 便于项目升级，无论是项目本身升级还是项目使用的依赖升级。
5. 有助于多模块项目的开发，一个模块开发好后，发布到仓库，依赖该模块时可以直接从仓库更新，而不用自己去编译。
6. maven有很多插件，便于功能扩展，比如生产站点，自动发布版本等

**缺点如下：**

1. maven是一个庞大的构建系统，学习难度大
2. maven采用约定优于配置的策略（convention over configuration），虽然上手容易，但是一旦出了问题，难于调试。
3. 当依赖很多时，m2eclipse 老是搞得Eclipse很卡。
4. 中国的网络环境差，很多repository无法访问，比如google code， jboss 仓库无法访问等。



# Maven的生命周期

1、clear：移除上一次构建生成的文件

2、compile：编辑项目的源代码

3、test：使用单元测试框架进行测试，测试代码不会被打包或者部署

4、package：接受编译好的代码，打包成可发布的格式

5、install：将包安装到maven本地仓库，供本地其他maven项目使用

6、deploy：将最终的包发布到远程仓库，供其他开发人员和maven项目使用。

7、site：生成项目的站点文档

# Maven的依赖

 1、依赖的传递性

 一个复杂的项目会包含很多依赖，也有可能包含依赖其他构件的依赖。这个是Maven的强大特征之一，它支持了传递性依赖。

 依赖原则：路径优先原则，第一声明原则。

 解决依赖问题：

通过调整dependency的顺序来解决：那个依赖的顺讯在前面就依赖那个

自己添加一个denpendeny来解决：因为该路径是最小的

通过exclusions元素排除不想要的传递性依赖。

2、Version解释

 snapshot版本表示不稳定，处于开发中的版本，即快照版本

 release：表示趋于稳定，当前停止更新，可以用于发行的斑斑。

场景：协同开发时，如果A依赖B，由于B频繁更新，B应该使用SNAPSHOT来标识自己。

3、依赖范围Scope

compile（默认）：编译范围的依赖，它在编译和打包的时候都会把该依赖打包进去

test：测试依赖范围，它在编译和打包的时候不会把该依赖打包进去

provided：在编译和测试范围有效，最后生成war包时不会打包进去

rumtime：运行时依赖，编译的时候不依赖

import：导入依赖范围

system：系统依赖范围

# maven依赖机制





# 依赖冲突--解决

#  本地jar包安装

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



# maven 项目无法识别、 maven 工具View 无法打开

``` txt
右击pom文件，add as maven project
```

