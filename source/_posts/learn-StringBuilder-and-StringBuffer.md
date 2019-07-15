---
title: 学习StringBuilder、StringBuffer
date: 2019/7/15
categories:
    - 技术总结
tags:
    - Java
---

## String基本知识

* 对String对象的任何改变都不影响到原对象，相关的任何change操作都会生成新的对象

比如对String+"hello"，就会new一个新的对象出来，把原来的字符串加上hello之后的值给新的。但是如果这个String要改变的次数很多的话，内存就会爆炸。
* String类其实是通过char数组来保存字符串的

* String str = "hello world" 与 String str = new String("hello world") 的区别

使用new创建对象是在堆区进行的，每次new出来的都是不同的对象

使用 = "hello world"，在编译期间生成了**字面常量和字符引用**，这部分叫做class文件常量池；在运行期间对应着方法区的运行时常量池（相同的字符串内容只保留一份）。

如果之后再用="hello world"，JVM执行引擎会在运行时常量池查找是否存在相同的字面常量，有相同的就把这个引用给它；没有的话，就在运行时常量池开辟空间存储字面常量。

## StringBuilder 与 StringBuffer

由于String改变内容的操作都要new一个新的对象，那么如果碰到需要很多次改变的情况，就用StringBuilder，其中的append方法操作是在原有对象的基础上进行的。

StringBuilder和StringBuffer类拥有的成员属性以及成员方法基本相同，区别是StringBuffer类的成员方法前面多了一个关键字：synchronized，不用多说，这个关键字是在多线程访问时起到安全保护作用的,也就是说StringBuffer是线程安全的。

执行效率：

StringBuilder > StringBuffer > String

当字符串相加操作或者改动较少的情况下，建议使用 String str="hello"这种形式；

当字符串相加操作较多的情况下，建议使用StringBuilder，如果采用了多线程，则使用StringBuffer。

## 面试题

1. 下面这段代码的输出结果是什么？

`String a = "hello2"; 　　String b = "hello" + 2; 　　System.out.println((a == b));`

　　输出结果为：true。原因很简单，"hello"+2在编译期间就已经被优化成"hello2"，因此在运行期间，变量a和变量b指向的是同一个对象。

2.下面这段代码的输出结果是什么？

　　`String a = "hello2"; 　  String b = "hello";       String c = b + 2;       System.out.println((a == c));`

　　输出结果为:false。由于有符号引用的存在，所以  String c = b + 2;不会在编译期间被优化，不会把b+2当做字面常量来处理的，因此这种方式生成的对象事实上是保存在堆上的。因此a和c指向的并不是同一个对象。

3.下面这段代码的输出结果是什么？

　　`String a = "hello2";   　 final String b = "hello";       String c = b + 2;       System.out.println((a == c));`

　　输出结果为：true。对于被final修饰的变量，会在class文件常量池中保存一个副本，也就是说不会通过连接而进行访问，对final变量的访问在编译期间都会直接被替代为真实的值。那么String c = b + 2;在编译期间就会被优化成：String c = "hello" + 2;

4.下面这段代码输出结果为：

```java
public class Main {
    public static void main(String[] args) {
        String a = "hello2";
        final String b = getHello();
        String c = b + 2;
        System.out.println((a == c));
    }
     
    public static String getHello() {
        return "hello";
    }
}
```

输出结果为false。这里面虽然将b用final修饰了，但是由于其赋值是通过方法调用返回的，那么它的值只能在运行期间确定，因此a和c指向的不是同一个对象。


## [常用方法](http://www.runoob.com/java/java-stringbuffer.html)


## 资料
[探秘Java中的String、StringBuilder以及StringBuffer](https://www.cnblogs.com/dolphin0520/p/3778589.html)




