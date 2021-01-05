1.函数式接口  
=  

2.Lambda表达式  
=  

3.Stream  
=   

4.Java 8允许在接口中加入具体方法。  
接口中的具体方法有两种，default方法和static方法，identity()就是Function接口的一个静态方法。  
Function.identity()：返回一个输出跟输入一样的Lambda表达式对象
```java
    private static void identity() {
        Stream<String> stream = Stream.of("I", "love", "you", "too");
        Map<String, Integer> map = stream.collect(Collectors.toMap(Function.identity(), String::length));
        System.out.println(map);
    }

    //输出
    //{love=4, too=3, I=1, you=3}
```

















参考：  
1.https://www.cnblogs.com/liuxiaozhi23/p/10880147.html  
2.https://www.jianshu.com/p/cd694d2d8be5