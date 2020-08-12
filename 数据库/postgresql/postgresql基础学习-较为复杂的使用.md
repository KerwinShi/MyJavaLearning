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
2. 连接  
=  
* CROSS JOIN  
    交叉连接  
    `SELECT ... FROM table1 CROSS JOIN table2 ...`  
    将第一个表的每一行（x行）与第二个表的每一行（y行）进行匹配，得到结果为x*y行的表（数据量可能非常大，要谨慎使用）  
* INNER JOIN  
    内连接  
    `SELECT table1.column1, table2.column2... FROM table1 INNER JOIN table2 ON table1.common_filed = table2.common_field;`  
    把 table1 中的每一行与 table2 中的每一行进行比较，找到所有满足连接谓词的行的匹配对，满足的时候就合成一个结果行。(把符合匹配条件的两张表中的数据拼接起来)  
* LEFT OUTER JOIN  
    左外连接
    `SELECT ... FROM table1 LEFT OUTER JOIN table2 ON conditional_expression ...`    
    首先执行一个内连接，对于表 T1 中不满足表 T2 中连接条件的每一行，其中 T2 的列中有 null 值也会添加一个连接行，连接的表在 T1 中每一行至少有一行。（除了拼接满足条件的数据外，表1中没有在表2找到匹配数据的行也会出现在结果中，拼接上null值）
* RIGHT OUTER JOIN  
    右外连接  
    `SELECT ... FROM table1 RIGHT OUTER JOIN table2 ON conditional_expression ...`
    首先，执行内部连接。然后，对于表T2中不满足表T1中连接条件的每一行，其中T1列中的值为空也会添加一个连接行。这与左联接相反;对于T2中的每一行，结果表总是有一行。（除了拼接满足条件的数据外，表2中没有在表1找到匹配数据的行也会出现在结果中，拼接上null值）
* FULL OUTER JOIN  
    全外连接  
    `SELECT ... FROM table1 FULL OUTER JOIN table2 ON conditional_expression ...`  
    首先，执行内部连接。然后，对于表 T1 中不满足表 T2 中任何行连接条件的每一行，如果 T2 的列中有 null 值也会添加一个到结果中。此外，对于 T2 中不满足与 T1 中的任何行连接条件的每一行，将会添加 T1 列中包含 null 值的到结果中。（除了拼接满足条件的数据外，表1中没有在表2找到匹配数据的行也会出现在结果中，拼接上null值；同时，表2中没有在表1找到匹配数据的行也会出现在结果中，拼接上null值）  

3. UNION  
=  
合并两个或多个 SELECT 语句的结果（每个select语句结果必须有相同数量的列，列也必须拥有相似的数据类型，列数据的顺序也必须一致）
```
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

UNION

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```  
需要注意的是，UNION 操作符会选取不同的值，如果两个select结果又重复的数据行，使用UNION ALL  

4. NULL  
=  
NULL代表遗漏的未知数据，默认列可以存放NULL值。  
用IS NOT NULL和IS NULL进行判断。  

5. 别名
=  
可以对一张表或者一个字段的名称进行重命名，这个名称就叫该表或字段的别名，使用AS完成。
```
SELECT column1, column2.... FROM table_name AS  lias_name WHERE [condition];

SELECT column_name AS alias_name FROM table_name
WHERE [condition];
```

6. 触发器
=  
触发器是数据库的回调函数，会在指定的数据库事件发生的时候自动执行。
* 什么时候触发
   * 执行操作之前  
   * 执行操作之后
   * 更新操作
* 触发后怎么执行
   * FOR EACH ROW(每一行触发一次)
   * FOR EACH STATEMENT(每个语句标记的触发器执行一次)  
* WHEN 子句和触发器操作在引用 NEW.column-name 和 OLD.column-name 表单插入、删除或更新时可以访问每一行元素。其中 column-name 是与触发器关联的表中的列的名称。  
* 如果存在 WHEN 子句，PostgreSQL 语句只会执行 WHEN 子句成立的那一行，如果没有 WHEN 子句，PostgreSQL 语句会在每一行执行。  
* BEFORE 或 AFTER 关键字决定何时执行触发器动作，决定是在关联行的插入、修改或删除之前或者之后执行触发器动作。  
* 要修改的表必须存在于同一数据库中，作为触发器被附加的表或视图，且必须只使用 tablename，而不是 database.tablename。  
* 当创建约束触发器时会指定约束选项。

```
CREATE  TRIGGER trigger_name [BEFORE|AFTER|INSTEAD OF] event_name(INSERT DELETE UPDATE)
ON table_name [FOR EACH ROW]
[
 -- 触发器逻辑....
];


CREATE  TRIGGER trigger_name [BEFORE|AFTER] UPDATE OF column_name
ON table_name
[
 -- 触发器逻辑....
];

查看触发器：
SELECT * FROM pg_trigger;

SELECT tgname FROM pg_trigger, pg_class WHERE tgrelid=pg_class.oid AND relname='company';

删除触发器：  
drop trigger ${trigger_name} on ${table_of_trigger_dependent};

```  

7. 索引
=  
加速搜索引擎检索数据的一种特殊表查询，索引有助于加快 SELECT 查询和 WHERE 子句，但它会减慢使用 UPDATE 和 INSERT 语句时的数据输入。索引可以创建或删除，但不会影响数据。  

```
单列索引
CREATE INDEX index_name ON table_name (column_name);
组合索引
CREATE INDEX index_name ON table_name (column1_name, column2_name);
唯一索引:不仅是为了性能，同时也为了数据的完整性,唯一索引不允许任何重复的值插入到表中。
CREATE UNIQUE INDEX index_name on table_name (column_name);
局部索引:表的子集上构建的索引；子集由一个条件表达式上定义。
CREATE INDEX index_name on table_name (conditional_expression);
隐式索引:
在创建对象时，由数据库服务器自动创建的索引。索引自动创建为主键约束和唯一约束。
删除索引
DROP INDEX index_name;
```  

使用注意事项：
* 索引不应该使用在较小的表上。
* 索引不应该使用在有频繁的大批量的更新或插入操作的表上。
* 索引不应该使用在含有大量的 NULL 值的列上。
* 索引不应该使用在频繁操作的列上。

https://www.runoob.com/postgresql/postgresql-join.html













