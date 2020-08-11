一、数据库操作
=  
1.1 对数据库的操作
-  
创建数据库  
`CREATE DATABASE dbname;  `  
选择数据库  

删除数据库  
`DROP DATABASE [ IF EXISTS ] name; ` 

1.2 对表的操作  
-  
创建表
```  
CREATE TABLE table_name(
   column1 datatype NOT NULL,
   column2 datatype,
   column3 datatype,
   .....
   columnN datatype,
   PRIMARY KEY( 一个或多个列 )
);  
```
说明：  
NOT NULL表示该字段要求非空  
PRIMARY KEY表示该字段为主键


删除表  
`DROP TABLE table_name;`  

1.3postgresql模式（SCHEMA）
-  
可以看作一个表的集合，一个模式可以包含视图、索引、据类型、函数和操作符等。  
相同的对象名称可以被用于不同的模式而不会出现冲突。  

关于模式暂时不做深入研究，目前将其理解为等价于一个数据库用户。（后续继续学习数据库的时候补充）  
常用的操作sql语句有  
创建一个模式  
```
CREATE SCHEMA myschema.mytable (
...
);
```  
删除一个模式  
`DROP SCHEMA myschema;`  
删除一个模式以及其中包含的所有对象  
`DROP SCHEMA myschema CASCADE;`  





