---
title: Java中常用API
date: 2019/3/18
categories:
    - 技术总结
tags:
    - Java
---
## 转换类型
* String转Char[]:

```java
    Char[] a = string。toCharArray();
```

* Char[]转String

```java
    //只能用
    return new String(char[])
```

## String操作

```java
    //创建String
    StringBuilder res = new StringBuilder();
    //添加字符
    res.append(something);
    //转换
    String(res) stringbuilder.toString()
    //reverse
    res.reverse();
    //去掉首位空格
    res.trim();
    //取char
    string.charAt(int i)
    //大小写转变
    toLowerCase()
    toUpperCase()
```

## 数字操作

```java
    //sqrt
    Math.sqrt(num);
```
