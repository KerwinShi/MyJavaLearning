1. 约束  
=  
约束用于规定表中的数据规则。  
如果存在违反约束的数据行为，行为会被约束终止。  
约束可以在创建表时规定（通过 CREATE TABLE 语句），或者在表创建之后规定（通过 ALTER TABLE 语句）。  
约束确保了数据库中数据的准确性和可靠性。  

常用约束：
```
NOT NULL：指示某列不能存储 NULL 值。  
UNIQUE：确保某列的值都是唯一的。  
PRIMARY Key：NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。  
FOREIGN Key： 保证一个表中的数据匹配另一个表中的值的参照完整性。  
CHECK： 保证列中的值符合指定的条件。  

示例：
CREATE TABLE COMPANY6(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL    CHECK(SALARY > 0)
);

CREATE TABLE DEPARTMENT1(
   ID INT PRIMARY KEY      NOT NULL,
   DEPT           CHAR(50) NOT NULL,
   EMP_ID         INT      references COMPANY6(ID)
);
 
EXCLUSION ：排他约束，保证如果将任何两行的指定列或表达式使用指定操作符进行比较，至少其中一个操作符比较将会返回 false 或空值。  
示例：
CREATE TABLE COMPANY7(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT,
   AGE            INT  ,
   ADDRESS        CHAR(50),
   SALARY         REAL,
   EXCLUDE USING gist
   (NAME WITH =,  -- 如果满足 NAME 相同，AGE 不相同则不允许插入，否则允许插入
   AGE WITH <>)   -- 其比较的结果是如果整个表边式返回 true，则不允许插入，否则允许
);

删除约束：  
ALTER TABLE table_name DROP CONSTRAINT some_name;
```  



https://www.runoob.com/postgresql/postgresql-join.html













