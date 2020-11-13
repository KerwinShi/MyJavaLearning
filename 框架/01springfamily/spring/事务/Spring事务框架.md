基本原则：让事务管理的关注点与数据访问关注点分离  
![事务管理编程模型](../../../../image/spring/spring事务管理编程模型.png)    

一、spring事务管理实现过程理解（统一中原）  
=  
核心接口：PlatformTransactionMannger(为应用程序提供事务界定的统一方式)，具体的实施由相应的实现类实现（不同的数据访问方式，全局事务场景）。  

实现的思路：  
以JDBC为例进行说明，JDBC是利用Connection实现事务管理的，所以在一个事务中，需要保证是同一个Connection（connection-passing）  
![connection-passing](../../../../image/spring/connection-passing.png)    
但是，这么做，就写死了connection，要换成hibernate的session怎么办？？？（但是这种想法是对的）  
![数据访问流程](../../../../image/spring/数据访问流程.png)  
这里引进TransactionResourceManager，对数据访问连接资源进行管理（一艘大船（具体是什么船与具体的核心接口实现类相关），要访问数据的时候，可以直接访问这艘船）

上述过程的代码实现原型  
1 核心接口（战略蓝图）：
![PlatformTransactionMannger接口](../../../../image/spring/PlatformTransactionMannger接口.png)  

2 数据访问控制（建造大船）：
![TransactionResourceManager](../../../../image/spring/TransactionResourceManager.png)  

3 核心接口的JDBC的实现类（实现对应的事务管理，确定是一艘什么船）：  
![JDBC核心接口实现类](../../../../image/spring/JDBC核心接口实现类.png)  

4 获取数据库连接并使用（访问大船）：  
![获取JDBC连接并使用](../../../../image/spring/获取JDBC连接并使用.png)  

二、大树底下好乘凉，也要看看大树张什么样子（和平年代）  
=  
从核心接口里面可以看出来，主要的接口有三个  
1.TransactionDefinition  
-  
事务属性定义：事务隔离级别、传播行为、超时时间、是否只读  
### 1.1隔离级别  

### 1.2传播行为  

### 1.3超时时间  

### 1.4是否只读  


2.TransactionStatus  
-  


3.PlatformTransactionMannger  
-  
