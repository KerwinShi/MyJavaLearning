Optional类  
Java 8 引入了一个新的容器类 Optional，可以代表一个值存在或不存在，避免空指针异常的出现。  

Optional 类比较常用的几个方法有：
```
isPresent() ：值存在时返回 true，反之 flase
get() ：返回当前值，若值不存在会抛出异常
orElse(T) ：值存在时返回该值，否则返回 T 的值
```