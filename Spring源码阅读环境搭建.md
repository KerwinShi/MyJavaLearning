为方便后续阅读Spring源码，利用idea完成源码阅读环境的搭建。特此记录搭建过程，方便后续回忆搭建过程。

1. Gradel安装  
=  
由于spring是利用Gradle搭建的，因此需要先安装Gradle。

都是类似的操作，下载-安装-配置环境变量（百度即可）（这里需要注意的是为了提高下载速度，可以配置一下镜像）

2. 获取Spring源码
=  
github找到项目源码地址（网上说github地址比较慢，其实也还行，不是那么慢）  
`git@github.com:spring-projects/spring-framework.git`  

git clone项目源码

3. idea打开项目，并构建  
=

由于idea会去下载gradle，但是由于网速等原因往往会失败，这时需要配置本地安装的gradle
操作：file-setting-搜索gradle
在如下的界面进行设置，设置效果如图所示：  
![idea集成gradle](./image/gradle/idea集成gradle.png "idea集成gradle")

idea打开克的项目，然后就是漫长的等待过程。。。。。。真的非常漫长BUILD SUCCESSFUL in 33m 46s     
![完成效果](./image/gradle/完成效果.png "完成效果")


由于官方为了避免冲突，排除了两个jar：  
spring-cglib-repack.jar  
spring-objenesis-repack.jar  

解决办法：  
idea直接利用gradle插件，双击对应jar