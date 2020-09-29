1.Redis分布式锁实现方式   
=  
1.1Jedis框架  
-  
Jedis是Redis的Java实现的客户端，其API提供了比较全面的Redis命令的支持。  
[Jedis框架](./Redis分布式锁框架/Jedis框架.md)
1.2Redisson框架  
-  
Redisson实现了分布式和可扩展的Java数据结构，功能较为复杂，不仅支持字符串操作，且还支持排序、事务、管道、分区等Redis特性；宗旨是促进使用者对Redis的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。
[Redisson框架](./Redis分布式锁框架/Redisson框架.md)


2.实现框架对比  
=  





参考：  
1.https://cloud.tencent.com/developer/article/1594456 \[Jedis与Redisson比较\]
