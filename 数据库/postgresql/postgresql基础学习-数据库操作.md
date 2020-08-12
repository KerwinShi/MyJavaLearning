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

此外，添加、修改、删除表操作还有：
* ALTER TABLE table_name ADD column_name datatype;（向存在的表中添加列）
* ALTER TABLE table_name DROP COLUMN column_name;（删除表中的列）
* ALTER TABLE table_name ALTER COLUMN column_name TYPE datatype;（修改表中列的数据类型）
* ALTER TABLE table_name MODIFY column_name datatype NOT NULL;（添加列的非空约束）
* ALTER TABLE table_name ADD CONSTRAINT MyUniqueConstraint UNIQUE(column1, column2...);（添加唯一约束）
* ALTER TABLE table_name ADD CONSTRAINT MyUniqueConstraint CHECK (CONDITION);
* ALTER TABLE table_name
ADD CONSTRAINT MyPrimaryKey PRIMARY KEY (column1, column2...);
* ALTER TABLE table_name DROP CONSTRAINT MyUniqueConstraint;
* ALTER TABLE table_name DROP CONSTRAINT MyPrimaryKey;  
* TRUNCATE TABLE  table_name;（TRUNCATE TABLE 用于删除表的数据，但不删除表结构）

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


1.4视图
-  
View（视图）是一张假表，只不过是通过相关的名称存储在数据库中的一个 PostgreSQL 语句。
用户或用户组认为更自然或直观查找结构数据的方式。  
限制数据访问，用户只能看到有限的数据，而不是完整的表。  
汇总各种表中的数据，用于生成报告。  

```
CREATE [TEMP | TEMPORARY] VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];

DROP VIEW view_name;
```  

1.5事务
-  
事务具有以下四个标准属性（ACID）
原子性（Atomicity）：事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行。  
一致性（Consistency）：事务应确保数据库的状态从一个一致状态转变为另一个一致状态。一致状态的含义是数据库中的数据应满足完整性约束。  
隔离性（Isolation）：多个事务并发执行时，一个事务的执行不应影响其他事务的执行。  
持久性（Durability）：已被提交的事务对数据库的修改应该永久保存在数据库中。  

```
BEGIN;
BEGIN TRANSACTION;

COMMIT;
END TRANSACTION;

ROLLBACK;
```  