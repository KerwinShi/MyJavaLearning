为方便后续阅读Spring源码，利用idea完成源码阅读环境的搭建。特此记录搭建过程，方便后续回忆搭建过程。

1. Gradel安装  
=  
由于spring是利用Gradle搭建的，因此需要先安装Gradle（最好安装与编译需要的版本一直，编译需要的版本在idea打开源码，或者源文件根目录下cmd 执行`gradlew.bat`的时候自动下载）。

修改仓库地址：  
cmd `gradle -g D:/Cache/.gradle build build`(测试通过)
https://blog.csdn.net/yanzi1225627/article/details/52024632

2. 获取Spring源码
=  
github找到项目源码地址（网上说github地址比较慢，其实也还行，不是那么慢）  
`git@github.com:spring-projects/spring-framework.git`  

git clone项目源码

在拿到代码后，修改文件build.gradle  
文件开始位置添加
```
buildscript {
	repositories {
		maven { url "https://repo.spring.io/plugins-release" }
        maven { url' http://maven.aliyun.com/nexus/content/groups/public/' }
        maven { url' http://maven.aliyun.com/nexus/content/repositories/jcenter'}
	}
}
```  
找到repositories添加镜像地址，加速下载
```
//新增以下2个阿里云镜像
maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter' }
maven { url "http://maven.aliyun.com/nexus/content/groups/public" }
maven { url "http://af.hikvision.com.cn:80/artifactory/maven-down" }
 //这是原来就在的
 mavenCentral()
maven { url "https://repo.spring.io/libs-spring-framework-build" }
maven { url "https://repo.spring.io/snapshot" } // Reactor and RSocket
//新增spring插件库
maven { url "https://repo.spring.io/plugins-release" }
```  

3. idea打开项目，自动构建项目  
=

打开项目可能出现的问题：
gradle代码报错  
解决办法：  
![buildgradle文件报错1](./image/gradle/buildgradle文件报错解决1 "buildgradle文件报错")
![buildgradle文件报错2](./image/gradle/buildgradle文件报错解决2 "buildgradle文件报错")

按照参考网站[1]上的做法，在打开idea进行自动构建之前，在cmd窗口进行gradle命令操作（2020版idea不执行也可以，一样的效果）  
`gradlew.bat`  

这时也会由于网速出现各种错误，记录一个坑：由于下载的gradle文件有缺失（网络原因导致），执行命令会得到无法解压的报错信息unzip。。。。。。  
（解决办法：按照提示，找到对应文件夹，删除重新下载）  

由于idea在gradle的作用下去下载各种jar包，但是由于网速等原因往往会失败，报各种各样的错。  

配置本地安装的gradle
操作：file-setting-搜索gradle
在如下的界面进行设置，设置效果如图所示：  
![idea集成gradle1](./image/gradle/idea集成gradle0.png "idea集成gradle1")
![idea集成gradle2](./image/gradle/idea集成gradle.png "idea集成gradle2")

idea打开项目开始构建项目，然后就是漫长的等待过程。。。。。。真的非常漫长（时间长，错误多）  
等待构建过程中，由于网络的原因会报各种各样的错误，重新构建就完事了，错误实在是千奇百怪，记不过来。（不断地尝试重新构建，要坚信源码是没有问题的，有问题的是你的网络）

项目源码出现了如图所示的蓝点，说明项目构建完成。  
![完成效果](./image/gradle/完成效果.png "完成效果")


4.编译源码  
=
上述过程只是完成了项目的构建，还需要进一步编译，才算是真正完成spring源码的编译。  




由于官方为了避免冲突，排除了两个jar：  
spring-cglib-repack.jar  
spring-objenesis-repack.jar  

解决办法：  
idea直接利用gradle插件，双击对应jar


[1] https://blog.csdn.net/smart_an/article/details/107199151
[2] https://blog.csdn.net/duli3554197/article/details/105799853/