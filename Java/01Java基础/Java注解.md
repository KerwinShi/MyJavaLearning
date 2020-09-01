注解：  
注解和注释很像；  
注释是给人看的，注解是给机器看的；  
为什么这么说  
spring框架里面运用了大量的注解，这些注解我们大部分情况下只是知道它是用来干什么的，但是功能不知道是如何实现的，虽然我们程序员不知道，但是有一段专门的程序知道，当这段程序看见对应的注解的时候，他就知道要去干什么事。  

上面内容里，需要明确的是：  
对于注解，是有专门的程序去读取它，解析它，然后根据得到的消息去执行相应的操作。  


1.注解的简单分类  
* 自定义注解
* JDK内置注解
* 框架中的注解


2.本质
注解的本质是一个接口
```
public @interface Test {
}
```

3.元注解
元注解就是注解的注解，目的是对注解进行规范，不能让自定义的注解随便乱来。
* @Retention：解释说明了这个注解的的存活时间。  
    RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。  
    RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中。  
    RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。  
    [@Retention](../../image/java基础/元注解retention.png "@Retention")
* @Documented：将注解中的元素包含到 Javadoc 中去。  
* @Target：指定了注解运用的地方。（限定注解的使用范围）  
    ElementType.ANNOTATION_TYPE 可以给一个注解进行注解  
    ElementType.CONSTRUCTOR 可以给构造方法进行注解  
    ElementType.FIELD 可以给属性进行注解  
    ElementType.LOCAL_VARIABLE 可以给局部变量进行注解  
    ElementType.METHOD 可以给方法进行注解  
    ElementType.PACKAGE 可以给一个包进行注解  
    ElementType.PARAMETER 可以给一个方法内的参数进行注解  
    ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举  
* Inherited：注解 Test 被 @Inherited 修饰，之后类 A 被 Test 注解，类 B 继承 A,类 B 也拥有 Test 这个注解。  
* @Repeatable：重复使用注解（没有这个元注解之前如果想要实现注解的重复使用要先有一个用来保存被重复使用的注解的注解），这个元注解加强了以后使用更方便了(直接上代码比较直观)  
有@Repeatable之前，是这么干的  
```java
//用来保存被重复使用的注解的注解
@Retention(RetentionPolicy.RUNTIME)
public @interface ReviewRecords {
    ReviewRecord[] value();
}

//被重复使用的注解
public @interface ReviewRecord {
  String name();//注解的属性，看上去像一个方法
  String date();
  String comment() default "";//具有默认值，使用的时候就可以不用自己指定值
}

//使用，看这里要写出上面的两个注解
  @ReviewRecords({
    @ReviewRecord(name="Eric", date="2019-12-19", comment="No comment"),//这里可以看出给多个属性赋值的时候要指明赋值给谁，只有一个，且没有指明的话就会赋值给value
    @ReviewRecord(name="Joan", date="2019-12-29")
  })
  public void method1(String name) {
    System.out.println("Hello, " + name);
  }
```  
改用@Repeatable之后，是这样式的
```java
//被重复使用的注解
@Repeatable(ReviewRecords.class)
public @interface ReviewRecord {
  String name();
  String date();
  String comment() default "";;
}

//使用,不需要再把注解ReviewRecords也写出来了，使用更为简单了
@ReviewRecord(name="Eric", date="2019-12-19", comment="No comment")
@ReviewRecord(name="Joan", date="2019-12-29")
public void method1(String name) {
System.out.println("Hello, " + name);
}
```
有了上面的知识，我们其实已经基本可以自己定义一个注解了，这里还要说一下注解的属性，这些属性
基本数据类型  
String  
枚举  
Class  
注解类型  
数组  

4.基本的使用  
主要分为三步  
* 定义注解
* 使用注解
* 读取并执行相应流程（这个就需要阅读源码了，主要是通过反射读取）

参考：
1.https://blog.csdn.net/sinat_33921105/article/details/107719604
2.https://cloud.tencent.com/developer/article/1579167











