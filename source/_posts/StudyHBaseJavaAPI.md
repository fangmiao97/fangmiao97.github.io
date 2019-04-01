---
title: 学习HBase JAVA API
date: 2019/03/28
categories: 
    - 技术总结
tages:
    - HBase
---
## 网站
* [HBase with Java](https://www.baeldung.com/hbase)
* [HBase表概念](https://www.bilibili.com/video/av21911076)
* [Filter 过滤器](https://www.jianshu.com/p/fb4bd7a2a23e)

## 概念
* Column Family
    * 列族是列的集合
    * 表可以有一个或多个列族
    * 属于同一个列族的所有列前缀相同
        * containInfo:fname
        * containInfo:lname
    * 表按照列族维度进行存储，一个列族的数据存在一起
    * 对一个列族可以单独定义存储属性调优，比如：一个列族存储图片数据，那么就可以对这个列族进行存储压缩
    * 一个列族想有多少列都可宜，在进行数据添加操作的时候插入即可。不用修改表定义，在创建表的时候，定好列族就可以
    *设计原则
        * 不经常在一起访问的数据分开设计，比如：一个用户信息的表，可以将用户名、真实姓名、花名放在一个列族，将头像放在另一个列族
        * 使用不同列族存储设置时分开设计
 
* Columns 列
    * 列中包含表的数据
    * 可以动态实时地创建
    
* Rows 行
    * 每一行有一个行键（RowKey)
    * RowKey类似于关系型数据库的主键
    * RowKey设计原则：方便scan操作，结合具体业务
    * 行按照行键排序进行存储，这样就提高了检索效率
    
* Cell 单元
    * 行与列的交叉点
    * 版本化，支持多个版本，更新之前的数据可以保存（要定义），比如，QQ头像可以看到原来的头像
    * 内容是不可分割的
    * 存储的是byte array字节数组，无字段类型。只要能转成byte array的都可以存
    * 单元为空时，物理上是不存储的
