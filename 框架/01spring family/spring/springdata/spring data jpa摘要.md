JPA(Java Persistence API)  
1. 一套API标准  
2. 面向对象的查询语言  
3. ORM元数据的映射（XML与JDK5注解）  

总的来说，JPA的宗旨是为POJO提供持久化标准规范。


最重要的就是7个接口和2个实现类  
![继承体系](../../../../image/spring/springdata/jpa/jpa继承体系结构.png "继承体系")
接口：  
Repository  
CrudRepository  
PagingAndSortingRepository  
QueryByExampleExecutor  
JpaRepository  
JpaSpecificationExecutor  
QueryDslPredicateExecutor  

实现类：  
SimpleJpaRepository  
QueryDslJpaRepository


真正的JPA底层封装类
EntityManager（javax.persistence）  
EntityManagerImpl（org.hibernate.jpa.internal）  
