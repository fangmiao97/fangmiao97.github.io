---
title: innodb学习笔记
date: 2019/12/28
categories: 
    - 数据库
    - innodb
    - 总结
---
## 约束

### 数据完整性

关系型数据库系统和文件系统的一个不同点是，关系数据库本身能保证存储数据的完整性，不需要应用程序的控制，而文件系统一般需要在程序端进行控制

数据完整性3种形式：

* 实体完整性

保证表中有一个主键。在InnoDB存储引擎表中，用户可以通过定义PrimaryKey或Unique Key约束来保证实体的完整性。用户还可以通过编写一个触发器来保证数据完整性。

* 域完整性

    保证数据每列的值满足特定的条件。在InnoDB存储引擎表中，域完整性可以通过以下几种途径来保证：
    
    *选择合适的数据类型确保一个数据值满足特定条件。
    
    *外键（Foreign Key）约束。
    
    *编写触发器。
    
    * 还可以考虑用DEFAULT约束作为强制域完整性的一个方面
 
* 参照完整性

保证两张表之间的关系。InnoDB存储引擎支持外键，因此允许用户定义外键以强制参照完整性，也可以通过编写触发器以强制执行。
 
对于InnoDB存储引擎本身而言，提供了以下几种约束：
* Primary Key
* Unique Key
* Foreign Key
* Default 
* NOT NULL

### 创建约束

* create表的时候定义主键等约束

* 通过ALTER TABLE来创建约束

```sql
alter table test add unique key id_num(id_num)
```
