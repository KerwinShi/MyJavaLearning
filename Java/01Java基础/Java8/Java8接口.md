接口为了支持默认方法：  
引入了关键字[default](./Java8_default.md)  

接口与类相似，可以使用static关键字，拥有接口本身的方法，而与对象无关。  
```java
public interface InterfaceA {
    //接口的static方法，可以被继承和隐藏，但是无法被重写
    static void showStatic() {
        System.out.println("static方法");
    }
}

public class Test {
    public static void main(String[] args) {
        //接口名称.方法名称  直接调用
        InterfaceA.show();
    }
}
```  


接口  vs   抽象类  
[接口](../接口.md)：interface  
接口更多的被使用于使对象拥有某种行为（多实现，可以不断扩展）  
[抽象类](../抽象类.md)：abstract class  
抽象类更多的被使用于描述对象的特征（单继承，一旦抽象类确定了，扩展不易）   