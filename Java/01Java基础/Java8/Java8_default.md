default为java8引入的关键字，可以在接口内部包含默认的方法实现  

为什么加这个关键字：  
java8之前，java接口需要添加方法的时候，所有的实现类都要对应实现这个方法，但是对于有许多实现类的情况，这样操作是工作量巨大的。为了添加方法，但是不影响已有的是实现，就需要接口中可以有默认的方法实现。  
一个重要的使用场景就是在集合中的foreach方法  

```java
//简单使用的情形  
//接口1
public interface Interface1{
    //接口方法有默认实现
    default void helloWorld() {
        System.out.println("hi i'm from Interface1");
    }
}

//实现类1
public class MyImplement implements Interface1{
    public static void main(String[] args) {
        MyImplement myImplement = new MyImplement();
        //直接调用helloWorld()方法，不需要MyImplement实现方法后使用
        myImplement.helloWorld();
    }
}

//又来一个接口，恰好也有默认的实现方法helloWorld
//接口2
public interface Interface2{
    default void helloWorld() {
        System.out.println("hi i'm from Interface2");
    }
}

//实现类同时实现了接口1和接口2
//实现类2（错误实现）
// public class MyImplement2 implements Interface1,Interface2{
//     public static void main(String[] args) {
//         MyImplement2 myImplement2 = new MyImplement2();
//         //直接调用helloWorld()方法（该调用哪个接口的默认方法呢？我傻了）
//         myImplement2.helloWorld();
//     }
// }

//实现类2（正确实现）
public class MyImplement2 implements Interface1,Interface2 {
    //既然默认的区分不出来了，只好自己动手了，自己实现一个方法
    @Override
    public void helloWorld() {
        System.out.println("hi i'm from MyImplement");
    }

    public static void main(String[] args) {
        MyImplement2 myImplement2 = new MyImplement2();
        myImplement2.helloWorld();
    }
}

//要是继承了实现类，又实现了接口呢？类和接口中有同名方法，这时也有两个默认的实现方法，会使用哪一个呢？
//实现类3
public interface A {
	public default void b(){
		System.out.println("AAA");
	}
}
//这里如果C的b()方法也是接口的默认方法，则Test会与上面同时实现两个接口一样报错，需要自己实现一个b方法
public class C {
	public void b(){
		System.out.println("CCC");
	}
}
public class Test extends C implements A{
	
}

public static void main(String[] args) {
        Test test = new Test();
        test.b();
    }
```
