基础认知：  
枚举：  
一个类的对象是有限个，确定的，就称为枚举（一个特殊的类）；  
以前的实现方式：  
不使用枚举类之前，一般使用接口静态常量实现。  
>适用于：定义和维护一组常量的时候，枚举非常合适；如果只有一个对象，那么可以作为[单例模式](../03并发编程/单例模式.md)的一种实现方式。  

栗子：  
```java
public enum SeasonEnum {
	SPRING, SUMMER, AUTUMN, WINTER
}
```
其中，SPRING, SUMMER, AUTUMN, WINTER 是SeasonEnum的成员（final、public、static），每一个枚举类型成员都可以看作是枚举类的实例。  

特性汇总：  
枚举可以实现接口，但不能继承接口，也不能被继承。  
枚举类是final的，所以不能继承。  
枚举类的构造方法是私有的。  
枚举成员是static、final和public。  
枚举成员是枚举类的实例。    


>背后的秘密：  
>enum就和class一样，只是一个关键字，他并不是一个类;反编译, 可以看到，当我们使用enum来定义一个枚举类型的时候，编译器会自动帮我们创建一个final类型的类继承Enum类,所以枚举类型不能被继承；类中有几个属性和方法，都是静态的（static类型的属性会在类被加载之后被初始化，而Java类的加载和初始化过程都是线程安全的，[类加载](./Java基础学习(98)-类加载.md)与[类初始化](./Java基础学习(97)-类初始化.md)）


使用：  
定义枚举类最常见的作用就是比较变量是否相同  
使用字符串进行比较  
思路一：将字符串转换为枚举成员后与枚举成员比较  
valueOf(String)  
思路二：将枚举成员转换为字符串与字符串比较  
name()或者toString()  
>可以自定义构造函数与方法，成员使用新的构造函数构造（参数个数不定）；属性和函数需要定义在成员的下面，Java要求先定义enum成员实例，所以在enum实例序列最后加上一个分号，后面再添加方法。

使用正型与枚举成员比较  
ordinal()方法用于获取成员的索引位置  

>常用方法说明：  
>values() ： 以数组返回所有成员  
>vauleOf() ： 将字符串转换为枚举实例  
>name(): 返回成员的名字  
>toString()：返回成员的名字，可以覆写  
>compareTo()： 比较两个枚举成员定义时的顺序  
>ordinal() ： 获取枚举成员的索引位置  

参考：  
1.https://blog.csdn.net/oscar999/article/details/107193005/  
2.http://hollischuang.gitee.io/tobetopjavaer/#/basics/java-basic/enum-usage  
