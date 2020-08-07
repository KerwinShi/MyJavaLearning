为方便后续阅读Spring源码，利用idea完成源码阅读环境的搭建。特此记录搭建过程，方便后续回忆搭建过程。

1. Gradel安装（这步版本无所谓）  
=  
由于spring是利用Gradle搭建的，因此需要先安装Gradle（最好安装与编译需要的版本一直，编译需要的版本在idea打开源码，或者源文件根目录下cmd 执行`gradlew.bat`的时候自动下载）。

修改仓库地址：  
cmd `gradle -g D:/Cache/.gradle build build`(测试通过)
https://blog.csdn.net/yanzi1225627/article/details/52024632

2. 获取Spring源码（首先尝试最新版源码master）
=  
github找到项目源码地址（网上说github地址比较慢，其实也还行，不是那么慢，好吧，我错了，有的时候直接从github上拉源码真的绝望，还是用gitee上的镜像地址吧）  
`git@github.com:spring-projects/spring-framework.git`  

git clone项目源码

在拿到代码后，修改文件build.gradle  
文件开始位置添加
```
buildscript {
	repositories {
		maven { url "https://repo.spring.io/plugins-release" }
        maven { url' https://maven.aliyun.com/nexus/content/groups/public/' }
        maven { url' https://maven.aliyun.com/nexus/content/repositories/jcenter'}
	}
}
```  
找到repositories添加镜像地址，加速下载
```
//新增以下2个阿里云镜像
maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter' }
maven { url "https://maven.aliyun.com/nexus/content/groups/public" }
maven { url "https://其他的镜像地址" }
 //这是原来就在的
 mavenCentral()
maven { url "https://repo.spring.io/libs-spring-framework-build" }
maven { url "https://repo.spring.io/snapshot" } // Reactor and RSocket
//新增spring插件库
maven { url "https://repo.spring.io/plugins-release" }
```  

3. idea打开项目，自动构建项目  
=

按照参考网站[1]上的做法，在打开idea进行自动构建之前，在cmd窗口进行gradle命令操作（2020版idea不执行也可以，一样的效果）  
`gradlew.bat`  

可能会超时导致下载失败，多试几次就好了  

这时也会由于网速出现各种错误，记录一个坑：由于下载的gradle文件有缺失（网络原因导致），执行命令会得到无法解压的报错信息unzip。。。。。。  
（解决办法：按照提示，找到对应文件夹，删除重新下载）  



提前设置了idea的编码格式为utf-8

配置本地安装的gradle
操作：file-setting-搜索gradle
在如下的界面进行设置，设置效果如图所示：  
![idea集成gradle1](./image/gradle/idea集成gradle0.png "idea集成gradle1")
![idea集成gradle2](./image/gradle/idea集成gradle.png "idea集成gradle2")（实际没有怎么干，选用了默认的选项，即依据配置文件gradle-wrapper中指定的gradle操作进行）


由于idea在gradle的作用下去下载各种jar包，但是由于网速等原因往往会失败，报各种各样的错。  idea打开项目开始构建项目，然后就是漫长的等待过程。。。。。。真的非常漫长（时间长，错误多）等待构建过程中，由于网络的原因会报各种各样的错误，重新构建就完事了，错误实在是千奇百怪，记不过来。（不断地尝试重新构建，要坚信源码是没有问题的，有问题的是你的网络）   


项目源码出现了如图所示的蓝点，说明项目构建完成。  
![完成效果](./image/gradle/完成效果.png "完成效果")

spring源码构建过程就已经劝退了。。。。。。

在家里的网络顺畅的情况下，终于顺利走完这一步了（源代码用的是fork的代码）

4.编译源码  
=
上述过程只是完成了项目的构建，还需要进一步编译，才算是真正完成spring源码的编译。  


依据import-into-idea.md说的，

打开项目可能出现的问题（原因jdk没有指定，或者版本不对，我遇到的问题是由于版本过低（spring源码为master最新版，jdk为jdk1.8.0_121），导致缺乏对应的包，编译失败）：
gradle代码报错  
解决办法：  
![buildgradle文件报错1](./image/gradle/buildgradle文件报错解决1.png "buildgradle文件报错")
![buildgradle文件报错2](./image/gradle/buildgradle文件报错解决2.png "buildgradle文件报错")


需要先完成编译spring-oxm下的compileTestjava  

（第一次到这里的时候，各种报错，千奇百怪，第二次，可能因为需要下载的东西都有了，一次编译通过，但是输出控制台中有乱码warning）（目前没找到解决办法，似乎不影响，就继续往下走了）  
![控制台乱码](image\gradle\控制台乱码解决办法.png "控制台乱码")

由于官方为了避免冲突，排除了两个jar：  
spring-cglib-repack.jar  
spring-objenesis-repack.jar  

解决办法：  
idea直接利用gradle插件，双击对应jar    
![预编译](image\gradle\预编译.png "预编译")  

排除aspects模块，设置编译jdk版，进行项目编译  
![编译](image\gradle\编译spring项目.png "编译")  

![排除aspect](image\gradle\右键-选择unload.png "排除aspect")   

![排除aspect1](image\gradle\右键-ignore.png "排除aspect1")   

剩下的就是听天由命，等待了。。。。。。  

又fxxk失败了，我哭了，你呢？？？

这会问题出在message模块的编译过程中。。。  
io.rsocket.RSocketFactory.ClientRSocketFactory 错误，似乎是下载的io.rsocket包有问题，maven中央仓库没有找到对应版本的，换了一个低版本的，原来出错的地方不出错了（io.rsocket.RSocketFactory.ClientRSocketFactory找不到），但是有其他地方出错了（RSocketClient找不到了）

祝我自己和大家以后编译一切顺利！  
祝我自己和大家以后编译一切顺利！  
祝我自己和大家以后编译一切顺利！  

[1] https://blog.csdn.net/smart_an/article/details/107199151
[2] https://blog.csdn.net/duli3554197/article/details/105799853/