Stream知识与Lambda密切相关

1.流的概念  
=  
Stream中文称为 “流”，将集合转换为这么一种叫做 “流” 的元素序列，通过声明性方式，能够对集合中的每个元素进行一系列并行或串行的流水线操作。（指定处理规则，流帮你自动处理输入的数据）  
![流操作流程](../../../image/java基础/Java8/流操作.png)  
![流操作demo](../../../image/java基础/Java8/流操作demo.png)  

2.流与[集合](../Java集合/Java集合.md)
=  
区别：  
>首先，虽然集合中的元素可以进行添加删除等操作，但是其中的元素都是确定好的，计算好的；流不一样，按需进行计算，要多少，处理多少；  
>其次，集合的迭代是外部迭代，迭代过程中，我们需要对其中的每一条数据进行一条一条的处理（亲自去实现迭代的过程，并行还需要自己实现）；但是，流不用，流只要制定好规则就可以，他会自动的去执行迭代的过程（串行或者并行）；

**注意：**  
流和迭代器一样，都是一次性的，只能只用一次，否则会报错的。  


3.实践中学习  
=  
准备工作，准备好操作对象  
```java
public class Demo{
    private String demoName;
    private int num;
    //省略getter与setter方法，构造方法
}

List<Demo> list = new Arraylist<>();
list.add(new Demo("demo1",1));
list.add(new Demo("demo2",2));
list.add(new Demo("demo3",3));
```

3.1stream/paraStream()   
-  
该方法的作用就是将集合转换为流（并行的流）  
```java
List list = new ArrayList();
list.stream();
```

3.2filter(T -> boolean)  
-  
保留boolean为true的元素
```java
list = list.stream()
            .filter(demo -> demo.getNum() == 2)
            .collect(toList());//保留数字为2的元素
```

3.3distinct()  
-  
去除重复的元素，通过类的 equals 方法来判断两个元素是否相等  


3.4sorted()/sorted((T, T) -> int)  
-   
元素的类实现了 Comparable 接口，即有自己的排序规则，那么可以直接调用 sorted() 方法对元素进行排序;要不然，就需要调用 sorted((T, T) -> int) 实现 Comparator 接口
```java
//如果元素的类实现了 Comparable 接口
list = list.stream()
           .sorted()
           .collect(toList());
//没有实现 Comparable 接口
list = list.stream()
           .sorted((p1, p2) -> p1.getAge() - p2.getAge())
           .collect(toList());
//简化后效果相同
list = list.stream()
           .sorted(Comparator.comparingInt(Person::getAge))
           .collect(toList());
```

3.5limit(long n)与skip(long m)  
-  
limit(long n)：返回前 n 个元素  
skip(long m)：调过前m个元素  
这两个在一起可以玩出花样：去除集合中的第m到n个，怎么实现


3.6map(T -> R)  
-  
将流中的每一个元素 T 映射为 R（就是将一个对象转换成另一个对象，把老对象映射到新对象上），跟上collect输出一个流  
几个比较详细的[使用示例](https://blog.csdn.net/qq_43472877/article/details/104162295)  


3.7flatMap(T -> Stream<R>)  
-  
将流中的每一个元素 T 映射为一个流，再把每一个流连接成为一个流   
一个[例子](https://blog.csdn.net/mark_chao/article/details/80810030)，同时可以观察与map的区别  

3.8anyMatch(T -> boolean)、allMatch(T -> boolean)与noneMatch(T -> boolean)  
anyMatch(T -> boolean)： 是否有一个元素匹配给定的 T -> boolean 条件  
allMatch(T -> boolean)： 是否所有元素都匹配给定的 T -> boolean 条件  
noneMatch(T -> boolean)：是否没有元素匹配给定的 T -> boolean 条件  

3.9findAny() 和 findFirst()  
findAny()：找到其中一个元素  
findFirst()：找到第一个元素  
这两的使用上的[区别](https://blog.csdn.net/huanghanqian/article/details/102807972)  
值得注意的是，这两个方法返回的是一个 Optional<T> 对象，它是一个容器类，能代表一个值存在或不存在  

3.10





参考：  
1.https://www.jianshu.com/p/0bb4daf6c800?from=groupmessage  

