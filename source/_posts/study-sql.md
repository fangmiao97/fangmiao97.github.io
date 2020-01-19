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
