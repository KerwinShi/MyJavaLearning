1.原理
=  
Synchronized是利用锁的机制来实现同步的  
锁的两种特性：  
互斥性：同一时间只有一个线程可以只有某个对象锁，从而实现多线程中的协调机制（保证原子性）  
可见性：必须在确保锁被释放之前，堆共享变量所做的修改，对于接下来获得该锁的另一个线程是可见的，否则数据不一致  

2.用法  
=  
2.1同步方法　　
-  
### 2.1.1同步非静态方法  
```java
public Synchronized static void method(){}
```
### 2.1.2同步静态方法
```java
public Synchronized void method(){}
```
2.2同步代码块  
-  
### 2.2.1非静态方法的代码块(对象锁)   
this指的是当前对象，java中的每一个对象对会有一个monitor对象（被一个线程占有，计数+1，不为0\[0表示自由\]表示被其他线程占有，释放的时候-1），监视器  
```java
public className{
    public void method(){
        Synchronized(this){

        }
    }
}
```
### 2.2.1非静态方法的代码块(类锁)  
```java
public className{
    public void method(){
        Synchronized(className.class){

        }
    }
}
```  


3.原理解析  
=  
这里会用到两个分析工具（查看线程的状态）：  
* JDK自带的JCONSOLE  
* jsatck pid命令  

原理需要进行反编译文件：  
javap -v filename  

对于方法，标志为ACC_SYNCHRONIZED，对于代码块，标志分别为monitorenter与monitorexit（两个，分别为正常退出与异常退出）
![方法上的标志](../../image/java并发/synchronized方法上的标识.png)
![代码块上的标志](../../image/java并发/synchronized代码块上的标识.png)  

java1.6之前，synchronized是重量级锁（霸道总裁，一旦占用，别人就没希望了，只能等我主动放弃），但是1.6之后java虚拟机对其进行了优化，引入了偏向锁，轻量级锁