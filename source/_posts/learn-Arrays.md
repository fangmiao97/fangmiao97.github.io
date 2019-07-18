---
title: 学习 Class Arrays
date: 2019/07/17
categories: 
    - 技术总结
---

## Docs

[Class Arrays](https://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html)

## Method Summary

* asList

返回固定大小List

```java
List<String> stringList = Arrays.asList("hello", "NTU");

List<Integer> intList = Arrays.asList(1, 2, 3, 4);

```

* binarySearch

可以在整个数组中查找，也可以在一定范围内查找

支持Long/int/short/double/char/byte/float/object/T类型数组的查找

```java
//以int为例

//binarySearch(int[] a, int key)
int index = Arrays.binarySearch(new int[] { 1, 2, 3, 4, 5, 6, 7 }, 6);  

//binarySearch(int[] a, int fromIndex, int toIndex, int key)
int index = Arrays.binarySearch(new int[] { 1, 2, 3, 4, 5, 6, 7 }, 1, 6, 6);  
```

* copy & copyRange

```java
String[] names2 = { "Eric", "John", "Alan", "Liz" };  
  
//[Eric, John, Alan]  
String[] copy = Arrays.copyOf(names2, 3);  
          
//[Alan, Liz]  
String[] rangeCopy = Arrays.copyOfRange(names2, 2,  
                names2.length);  
```

* sort

升序排列

```java
String[] names = { "Liz", "John", "Eric", "Alan" };  
//只排序前两个  
//[John, Liz, Eric, Alan]  
Arrays.sort(names, 0, 2);  
//全部排序  
//[Alan, Eric, John, Liz]  
Arrays.sort(names);  
```

另外，Arrays的sort方法也可以结合比较器，完成更加复杂的排序。

* toString

返回数组内容的一个String

```java
String[] name = {"hahah", "hahah"};
//hahah，hahah
String s = Arrays.toString(name);
```

* deepToString

打印多维数组（multidimensional arrays）的内容

* equls

比较两个一维数组是否相等

```java
String[] names1 = { "Eric", "John", "Alan", "Liz" };  

String[] names2 = { "Eric", "John", "Alan", "Liz" };  
  
System.out.println(Arrays.equals(names1, names2));  
```

* deepEquals

判断更复杂的数组是否相等

* fill

向整个数组或一定范围内的数组填充指定内容
