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
    //字符串比较
    A.equals(B)
    //split任意数目个空格
    s.split("\\s+")
    //判断是否为空字符串，即 s = ""
    s.isEmpty()
    //判断字符是否是字母或数字
    char.isLetterOrDigit() = true/false
    if(( c >= 'a' && c <= 'z') && (c >= '0' && c <= '9'))
    //使用正则表达式替换字符串中的字符
    s.replaceAll(String regex, String replacement)
```

## Character

```java
    //是否为数字
    Character.isDigit(char c)
    //判断字符是否是字母或数字
        char.isLetterOrDigit() = true/false
```

## 数字操作

```java
    //sqrt
    Math.sqrt(num);
```

## 重要数据结构

### HashSet

```java
    //创建
    Set<Object> hSet = new HashSet();
    //添加元素
    hSet.add(somethin);
    //删除
    hSet.remove(object);
    //判断包含
    hSet.contains(object)
```
