为什么要学这个知识  
多线程中，为了保证数据一致性，我们学习到了synchronized、volatile、单例模式，这里面都涉及到了锁这个东西，CAS和AQS就是基础知识。  
锁：
悲观锁：写（增删改）操作多，读（查）操作少---Lock  
乐观锁：读（查）操作多，写（增删改）操作少---版本  



1.CAS
=  
compare and set/compare and swap  一种**无锁**的**原子**算法，乐观锁  
一个期望值，与现有的值比较，若相等，则进行修改；否则，什么事情都不做。
CAS（V(现在取到的数值)，E（期望值），N（修改后的值））  
V和E用来判断这个目标值是否被别人操作过，修改了  

作用：实现操作叫为复杂，但是无锁，不会阻塞，效率高，提高CPU的吞吐量，性能好  

```java
public class CasDemo{
    private static volatile int m = 0;
    private static AtomicInteger atomic = new AtomicInteger(0);
    public static void increase(){
        m++;
    }

    public static void increase2(){
        atomic.incrementAndGet();//++i
    }
    //结果还是异常，因为主程序先结束了
    // public static void main(String[] args){
    //     for(int i=0;i<20;i++){
    //         new Thread(()->{
    //             CasDemo.increase();
    //             CasDemo.increase2();
    //         }).satrt();
    //     }
    //     atomic.get();
    // }

    //结果都是20，但是实现原理是不也一样的
    public static void main(String[] args) throws InterruptedException{
        Thread[] t = new Thread[20];
        for(int i=0;i<20;i++){
            t[i] = new Thread(()->{
                CasDemo.increase();
            }); 
            t[i].start();
            t[i].join();//线程有交互性，
        }
        
        Thread[] tf = new Thread[20];
        for(int i=0;i<20;i++){
            tf[i] = new Thread(()->{
                CasDemo.increase2();
            }); 
            tf[i].start();
            tf[i].join();
        }

    }
    

}

```
![cas反编译结果](../../image/java并发/cas实现原理.png)

2.AQS
=  










3.自定义锁  
=  









