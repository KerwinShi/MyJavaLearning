根据jvm参数开头可以区分参数类型，共三类：“-”、“-X”、“-XX”：  
1. 标准参数（-）：所有的JVM实现都必须实现这些参数的功能，而且向后兼容；  
例子：-verbose:class，-verbose:gc，-verbose:jni……
2. 非标准参数（-X）：默认jvm实现这些参数的功能，但是并不保证所有jvm实现都满足，且不保证向后兼容；  
例子：Xms20m，-Xmx20m，-Xmn20m，-Xss128k……  
3. 非Stable参数（-XX）：此类参数各个jvm实现会有所不同，将来可能会随时取消，需要慎重使用；  
例子：-XX:+PrintGCDetails，-XX:-UseParallelGC，-XX:+PrintGCTimeStamps  


| 参数 | 描述 |  辅助记忆 | 
| :----:| :----: | :----: |
| -Xms | 为jvm启动时分配的内存 |memory size|
| -Xmx | 为jvm运行过程中分配的最大内存 |memory max|
| -Xss | 为jvm启动的每个线程分配的内存大小 |satck size|
| -Xmn | 新生代大小 |memory new|

>-Xms、-Xmx、-Xss三个参数的设置都是默认以Byte为单位的，也可以在数字后面添加[k/K]或者[m/M]来表示KB或者MB。而且，超过机器本身的内存大小也是不可以的，否则就等着机器变慢而不是程序变慢了。maxMemory()这个方法返回的是java虚拟机（这个进程）能构从操作系统那里挖到的最大的内存（-Xmx）；totalMemory()这个方法返回的是java虚拟机现在已经从操作系统那里挖过来的内存大小（启动的时候就会无条件的从操作系统中挖 -Xms后面定义的内存数）； freeMemory()是挖过来而又没有用上的内存。


参考：  
1.https://www.cnblogs.com/redcreen/archive/2011/05/04/2037057.html  
2.http://baijiahao.baidu.com/s?id=1663410856124616488&wfr=spider&for=pc