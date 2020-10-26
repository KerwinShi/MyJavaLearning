1.是什么  
=  
Lombok是一款利用注解简化我们开发过程中的一些通用的代码（比如简单java对象的构造函数、setter和getter等）的工具。  
>简化冗余的javabean代码；提高javabean中方法的执行效率  

一般就是直接添加maven依赖即可使用，为了在ide中更方便的使用，需要安装插件（idea插件库搜索-安装）   

2.怎么用  
=  
| 注解 | 作用描述 |
| :----:| :----: | 
| @Getter/@Setter | get与set方法 | 
|  @ToString | toString() 方法 |
|  @@EqualsAndHashCode | equals(Object other) 和 hashcode() 方法（在 Java 中有规定，当两个对象 equals 时，他们的 hashcode 一定要相同，当 hashcode 相同时，对象不一定 equals） |
|  @NoArgsConstructor, @AllArgsConstructor, @RequiredArgsConstructor | 自动生成该类的构造器，差别只在生成的构造器的参数不一样 |
|  @Data | 整合包，只要加了 @Data 这个注解，等于同时@Getter/@Setter@ToString@EqualsAndHashCode@RequiredArgsConstructor |
|  @Value | 整合包，但是他会把所有的变量都设成 final 的，其他的就跟 @Data 一样，@Getter@ToString@EqualsAndHashCode@RequiredArgsConstructor；@Value 注解，则是适合加在值不希望被改变的类上，像是某个类的值当创建后就不希望被更改，只希望我们读它而已 |
| @Builder | 自动生成流式 set 值写法 | 
| @Slf4j | 自动生成该类的 log 静态常量，要打日志就可以直接打，不用再手动 new log 静态常量了 | 










参考：  
1.https://blog.csdn.net/qq_36761831/article/details/86546970  
2.https://www.cnblogs.com/ooo0/p/12448096.html  