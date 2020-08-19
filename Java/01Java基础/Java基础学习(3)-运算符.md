总的来说：
几乎所有的运算符只能操作基本类型，但是=、==和!=例外（几乎可以操作所有对象），String类支持+，和+=

加减乘除没什么好说的  
赋值：（a=b）  
* b为基本类型：直接赋值给变量a  
* b为引用类型：把引用复制给a，指向同一个对象（将引用传递给方法的时候，仍然指向同一个对象）  
递增和递减  
对于前递增和前递减（如 ++a 或 --a），会先执行递增/减运算，再返回值;  
而对于后递增和后递减（如 a++ 或 a--），会先返回值，再执行递增/减运算。  
关系运算符:产生一个布尔类型的结果  
== 和 != 比较特殊，可以用于所有的基本类型（其他比较运算法不能用于boolean），而且可以用于测试对象的等价性。  
一些非常令人疑惑的问题就要向我们袭来，阅读下面的内容要保持清醒的头脑，阅读下面的内容要保持清醒的头脑，阅读下面的内容要保持清醒的头脑（重要的事情说三遍）：
1. ==和!=比较Integer
    ```
    // operators/Equivalence.java
    public class Equivalence {
        public static void main(String[] args) {
            Integer n1 = 47;
            Integer n2 = 47;
            System.out.println(n1 == n2);
            System.out.println(n1 != n2);
        }
    }

    输出结果：
    true
    false
    ```
    我们知道的是，==和!=比较的是引用，结果就该是false和true，但是和输出结果反了，这是因为Integer内部维护着一个IntegerCache的缓存，默认缓存范围是 [-128, 127]，没有像创建其他对象的时候那样创建两个对象。  
2. equal
    equal用来比较两个对象的内容是否相同。但是问题又出现了：
    ```
    // operators/EqualsMethod.java
    public class EqualsMethod {
        public static void main(String[] args) {
            Integer n1 = 47;
            Integer n2 = 47;
            System.out.println(n1.equals(n2));
        }
    }

    输出结果:
    true
    ```  
    毫无疑问。但是，但是，但是......
    ```
    // operators/EqualsMethod2.java
    // 默认的 equals() 方法没有比较内容
    class Value {
        int i;
    }

    public class EqualsMethod2 {
        public static void main(String[] args) {
            Value v1 = new Value();
            Value v2 = new Value();
            v1.i = v2.i = 100;
            System.out.println(v1.equals(v2));
        }
    }

    输出结果:
    false
    ```   
    WTF，对象内容不是一致的吗？  
    其实，是因为equals()的默认行为是比较对象的引用而非具体内容，除非在新的类中覆写equals()。

逻辑运算  
&& （AND）、||（OR）和 !（非）根据参数的逻辑关系生成布尔值 true 或 false  
逻辑运算中存在“短路”（整个表达式会在运算到可以明确结果时就停止并返回结果）

小知识：
1.Random  
```
// Create a seeded random number generator
//其中的47是种子（随机数生成器的初始化值，其始终为特定种子值产生相同的序列），每次启动程序，随机出来的结果是一致的
Random rand = new Random(47);
 int i, j, k;
// Choose value from 1 to 100:
//nextInt() 的参数设置生成的数字的上限，下限为零，为了避免零除的可能性，结果偏移1
j = rand.nextInt(100) + 1;
System.out.println("j : " + j);
k = rand.nextInt(100) + 1;
System.out.println("k : " + k);
```
