1. 绘制uml图    
ctrl + alt + u    
https://www.cnblogs.com/deng-cc/p/6927447.html    

2. 查看类的结构  
ctrl + f12  

3. 查看类的继承关系树  
shift + alt +  f12

4. 快速定位到上一次查看位置  
alt + 方向键

5. 查看方法被调用位置
alt + f7

6. 热部署设置（spring boot + idea community版）  
配置pom文件  
``` 
<!-- 热部署模块 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
   <optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
</dependency>

<plugin>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-maven-plugin</artifactId>
   <configuration>
      <fork>true</fork>
   </configuration>
</plugin>
```  
设置idea编译器--设置file→setting→compiler，勾选build project automatically

快捷键 ctrl + shift + alt + / 选择registry，勾选compiler.automake.allow.when.app.running，设置app运行过程中动态的编译保存的内容  






