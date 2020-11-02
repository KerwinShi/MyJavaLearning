官方文档：https://spring.io/projects/spring-kafka#learn  

spring kafka整合spring boot项目过程中遇到的问题记录：  
### 问题一.版本冲突问题  
由于spring kafka与spring boot整合的过程中是版本的新旧程度不一致，会导致无法初始化kafka相关对象的问题发生，诸如找不到类加载器，找不到方法等问题。  
>出现这些问题的时候，就先去[官方文档](https://spring.io/projects/spring-kafka#overview)看看，版本配合的问题（版本之间的配合真的是个坑啊，每个人的问题又不太一样，找博客什么的很费劲）   


### 问题二.配置文件配置项  

