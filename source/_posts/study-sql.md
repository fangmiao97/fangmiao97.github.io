---
title: sql要点复习
date: 2020/01/17
categories:
    - 数据库
tags:
    - SQL
---

## 外键与on delete cascade

building表和room表，由building_no键连接，并且设置on delete cascade。

这样在删除building时会通过级联将room中相关building的数据也删掉。楼倒了，屋子也就没了。

```
CREATE TABLE if NOT EXISTS buildings(
	building_no INT PRIMARY KEY AUTO_INCREMENT,
	building_name VARCHAR(255) NOT NULL,
	adds VARCHAR(255) NOT null
	);
	
CREATE TABLE if NOT EXISTS rooms(
	room_no INT PRIMARY KEY AUTO_INCREMENT,
	room_name VARCHAR(255) NOT NULL,
	building_no INT NOT NULL,
	FOREIGN KEY (building_no)
	REFERENCES buildings (building_no)
	ON DELETE cascade
	)
```

使用information_schema数据库中的referential_constraints表中查询cascade信息
```
SELECT 
    table_name
FROM
    referential_constraints
WHERE
    constraint_schema = 'testdb'
        AND referenced_table_name = 'buildings'
        AND delete_rule = 'CASCADE'
```

## update与select连用

更新salesRepEmployeeNumber为null的为其他新值

```
UPDATE customers
SET 
salesRepEmployeeNumber = (SELECT employeeNumber
FROM employees
WHERE jobTitle = 'Sales Rep'
LIMIT 1)
WHERE
salesRepEmployeeNumber IS NULL;
```

## 备份数据库mysqldump

```
mysqldump -u [username] –p[password] [database_name] > [dump_file.sql]
```

* username : 有效的mysql用户名
* password ：用户的有效密码
* database_name : 要备份的数据库名称
* dump_file.sql : 要生产的转储文件

如果只想备份数据库结构而不需要备份数据，那么只需要添加一个选项-no-data来告诉mysqldump只需要导出数据库结构

```
mysqldump -u [username] –p[password] –no-data [database_name] > [dump_file.sql]
```
## delete & truncate table

* 如果没有外键约束，truncate table语句将删除该表，并建立一个具有相同结构的新表。当是大块的表时，比delete语句效率高。
* 有外键约束时，truncate table逐行进行删除，并将子表中级联的数据项删除
* 有外键但是没有指定delete cascade，truncate 会逐行删除，遇到子表中的行引用时，停止并发生错误。
* truncate table没有用delete语句，所以不会触发delete触发器

## rename table & alter table

* alter table 完成很多表结构的修改（增删改）
* rename table lastname to newname 不可用于修改零时表（temporary table）
* alter table temporarytable rename to newname 可以修改零时表名

## MySQL数据类型

![](https://pic.downk.cc/item/5e214d4d2fb38b8c3c318370.jpg)

* decimal 定点数，账户余额（金融类）

## CTE Common table expression

mysql8.0新增公共表表达式CTE

公用表表达式是一个命名的临时结果集，仅在单个SQL语句(例如SELECT，INSERT，UPDATE或DELETE)的执行范围内存在。

```sql
WITH customers_in_usa AS (
	SELECT 
	    customerName, state
	from
	    customers
	where
	    country = 'USA'
)SELECT 
	customerName
FROM 
	customers_in_usa
where
	state = 'CA'
ORDER BY customerName;
```
### [遍历分层数据](https://www.yiibai.com/mysql/recursive-cte.html)

递归公用表表达式(CTE)是一个具有引用CTE名称本身的子查询的CTE

## 邻接列表模型管理MySQL中的分层数据

本表中的主键作为外键（自身循环作为外键），最顶层的外键为null。

## 比较两个表不同的数据

```sql
SELECT id,title,COUNT(*) FROM (
SELECT id, title FROM t1
UNION ALL
SELECT id, title FROM t2
) tb1
GROUP BY id,title
HAVING COUNT(*) = 2
ORDER BY id;
```

![](https://pic.downk.cc/item/5e25d2072fb38b8c3ca5a6bb.jpg)

## 查找表中重复数据


GROUP BY子句通过列或表达式的值将一组行分组为一个小分组的汇总行记录。 GROUP BY子句为每个分组返回一行。换句话说，它减少了结果集中的行数。

```sql
SELECT * FROM contacts;
SELECT first_name, COUNT(first_name)
FROM contacts
GROUP BY first_name
HAVING COUNT(first_name) > 2;
```
![](https://pic.downk.cc/item/5e27dc912fb38b8c3cdc278f.jpg)


多列重复值查询

```sql
SELECT 
	first_name, COUNT(first_name),
	second_name,COUNT(second_name),
	email, COUNT(email)
FROM 
	contacts
GROUP BY
	first_name,second_name,email
having
    count(first_name) > 2 and count (second_name) > 2;
```