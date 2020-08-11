常用的数据库SQL语句  
1. 增删改查  
=
* insert into
```  
插入单行数据
INSERT INTO table_name (col1, col2, col3, ...) VALUES (val1, val2, val3, ...);
插入多行数据
INSERT INTO table_name (col1, col2, col3, ...) VALUES (val1, val2, val3, ...), (val1, val2, val3, ...);

返回结果解释：
INSERT oid 1 ：成功插入1行目标表具有OID的数据，返回为插入的行数据对应的OID
INSERT 0 # ： 成功插入#行数据
```  
* delete  
```
删除满足指定条件的数据
DELETE FROM table_name WHERE CONDITION1
没有where条件则删除所有数据
```  
* update  
```
UPDATE table_name SET col1 = val1, col2 = val2, .... WHERE CONDITION1
```  
* select  
```
选择表中的对应字段信息
SELECT col1, col2, col3 FROM table_name;
选取表中所有字段的信息
SELECT * FROM table_name;

WHERE条件
SELECT column1, column2, columnN FROM table_name WHERE [condition1]  
多个condition之间通过AND, OR, 
IS NOT NULL    (非空), 
LIKE           (模糊查询，%XXX[左模糊]，XXX%[右模糊],_表示占一个字符位),
IN(CONDITION1，CONDITION2)  (满足条件CONDITION1，CONDITION2之一的),
NOT IN(CONDITION1，CONDITION2)  (同时不满足条件CONDITION1，CONDITION2的),
BETWEEN CONDITION1 AND CONDITION2 (满足在条件CONDITION1 和 CONDITION2之间的),
子查询 EXIST(子查询)[子查询结果有返回，则进行上一级的查询], 或者将子查询结果作为条件查询的一部分

LIMIT
限制查询数据的数量，配合OFFSET使用，表示从一个特定的偏移开始查询指定数量的数据
SELECT column1, column2, columnN FROM table_name LIMIT [no of rows] OFFSET [row num]

GROUP BY col1, col2, ...(必须放在where条件之后，order之前)对相同的数据进行分组

ORDER BY col1, col2, ...[ASC(升) | DESC(降) ] 对一列或者多列数据进行升序（ASC）或者降序（DESC）排列。

示例：
SELECT NAME, SUM(SALARY) FROM COMPANY GROUP BY NAME ORDER BY NAME DESC;

WITH 子句提供了一种编写辅助语句的方法，以便在更大的查询中使用。
示例：
With CTE AS (Select ID, NAME, AGE, ADDRESS, SALARY FROM COMPANY ) Select * From CTE;

HAVING 子句可以让我们筛选分组后的各组数据。
示例：
SELECT NAME FROM COMPANY GROUP BY name HAVING count(name) < 2; 

DISTINCT 关键字与 SELECT 语句一起使用，用于去除重复记录，只获取唯一的记录。
示例：
SELECT DISTINCT name FROM COMPANY;
```  
2. 运算符  
=  
* 算术运算法
```
加减乘除 + - * /
模 %
指数 ^
平方根 |/
立方根 ||/
阶乘 !(放在前面的时候为 !!)
```  
* 比较运算符  
```
等于
不等于 <> (建议使用)或者 !=
大于
小于
大于等于
小于等于
```  
* 逻辑运算  
```
AND 逻辑与
NOT 逻辑非
OR  逻辑或
```  
* 位运算  
```
& 按位与
| 按位或
# 按位异或
~ 按位取反
<< 二进制左移（左边抛弃，右边补0）
>> 二进制右移（右边抛弃，原来为正，左边补0，原来为负，左边补1）
``` 
3. 表达式  
=
* 布尔表达式  
根据指定条件来读取数据  
* 数字表达式  
查询语句中的数学运算：  
```
avg()
sum()
count()
```  
* 日期表达式  
返回当前相同的日期和时间：
`SELECT CORRENT_TIMESTAMP;`







