OGNL(Object-Graph Navigation Language)是一种表达式语言(EL)  

常见应用场景：  
传统的Struts框架  
MyBatis  
[Arthas](../开发工具/线上调试工具/ARTHAS.md)   

1. 对象访问：  
根据目标对象进行划分：
简单对象（基本数据类型）`即基本类型的对象访问，不加任何前缀，直接使用即可`  
非简单对象（实例访问）`#demo`    
静态对象（静态类）`@java.lang.Math`  

2. 方法访问：  
```
// 非基本对象的方法访问，#开头，对象与方法之间用.连接
#obj.method( 参数 )

// 静态对象的方法访问，@开头，对象与方法之间用@连接
@xxx@method( 参数 )

// 基本对象的方法访问，和非基本对象方法方式一致
"name".length()

```

3. 成员访问：  
```
// 非基本对象的成员访问，#开头，对象与成员之间用.连接
#obj.field

// 静态对象的成员访问，@开头，对象与成员之间用@连接
@xxx@field

// 基本对象的成员访问，和非基本对象成员方式一致
"name".hash
```


参考：
1.https://blog.hhui.top/hexblog/2019/11/29/191129-Ognl-%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/（博客）    
2.https://commons.apache.org/proper/commons-ognl/language-guide.html（官网）  
3.https://cloud.tencent.com/developer/article/1554323（实例）






