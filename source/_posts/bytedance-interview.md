---
title: 字节跳动12面
date: 2019/08/19
categories: 
    - 面试
---

## context
周日面了字节跳动大数据开发岗的前两面，第三面还没有约时间，我觉得有点要凉的意思。

不过也没啥，算是秋招的第一场面试，怎么可能一上来就过呢？没有啥是一帆风顺的，遇到坎才能认清自己。希望三面给我一锤，让我认清现实，好好复习。

先回顾一下自己的状态吧，现在只在LeetCode上面刷了题，操作系统、网络、java都没有系统的复习，每天还要上难的一批的课，哎，真不知道该怎么办。跟同学聊天说，人总是从一个小坑跳到另一个大坑，现在对你来说是大坑，等走到下个阶段就觉得仅此而已。

废话不说了，回忆一下前两面的题目：

## 一面

* 七层协议

![](https://puui.qpic.cn/fans_admin/0/3_1601981367_1566441221101/0)

应用层：各种应用程序协议，如HTTP、SMTP、FTP、POP3、S-HTTP

表示层：信息的语法语义以及它们的关联，如加密解密、转换翻译、压缩解压缩。

会话层：不同机器上的用户之间建立及管理会话。SSL安全套接字、TLS传输层安全协议、RPC

传输层：接受上一层的数据，在必要时把数据进行分割，并将这些数据交给网络层，且保证这些数据段有效到达对端。TCP传输控制协议、UDP用户数据报协议。

网络层：控制子网的运行、如逻辑编制、分组传输、路由选择。IP/IPv6（向上层提供可靠的端到端的通信）

数据链路层：物理寻址，同时将原始比特流转变成逻辑传输线路。ARP地址解析协议

物理层：机械、电子、定时接口通信信道上的原始比特流。IEEE802.2、Ethernet。

TCP协议和UDP协议特性区别总结：

     1. TCP协议在传送数据段的时候要给段标号；UDP协议不
     2. TCP协议可靠；UDP协议不可靠
     3. TCP协议是面向连接；UDP协议采用无连接
     4. TCP协议负载较高，采用虚电路；UDP采用无连接
     5. TCP协议的发送方要确认接收方是否收到数据段（3次握手协议）
     6. TCP协议采用窗口技术和流控制
     
* RPC与HTTP的区别

传输协议：

RPC，可以基于TCP协议，也可以基于HTTP协议

HTTP，基于HTTP协议

传输效率

RPC，使用自定义的TCP协议，可以让请求报文体积更小，或者使用HTTP2协议，也可以很好的减少报文的体积，提高传输效率

HTTP，如果是基于HTTP1.1的协议，请求中会包含很多无用的内容，如果是基于HTTP2.0，那么简单的封装以下是可以作为一个RPC来使用的，这时标准RPC框架更多的是服务治理

性能消耗，主要在于序列化和反序列化的耗时

RPC，可以基于thrift实现高效的二进制传输

HTTP，大部分是通过json来实现的，字节大小和序列化耗时都比thrift要更消耗性能

负载均衡

RPC，基本都自带了负载均衡策略

HTTP，需要配置Nginx，HAProxy来实现

服务治理（下游服务新增，重启，下线时如何不影响上游调用者）

RPC，能做到自动通知，不影响上游

HTTP，需要事先通知，修改Nginx/HAProxy配置

* linux指令

[find, grep, which, whereis, locate](https://blog.csdn.net/wzzfeitian/article/details/40985549)

不包含某了名字的文件：

find ! -name "abc"

* linux删除文件后为什么内存没释放？
[在Linux或者Unix系统中，通过rm或者文件管理器删除文件将会从文件系统的目录结构上解除链接(unlink).然而如果文件是被
  打开的（有一个进程正在使用），那么进程将仍然可以读取该文件，磁盘空间也一直被占用](https://blog.csdn.net/wyzxg/article/details/4971843)
  

* java并发编程（没听懂考官什么意思，不知道他想问的是创建线程的两个方式，还是其他的问题。我说了可以用Future，他好像没听清就下一题了）

创建线程的2种方式，一种是直接继承Thread，另外一种就是实现Runnable接口。

这2种方式都有一个缺陷就是：在执行完任务之后无法获取执行结果。

如果需要获取执行结果，就必须通过共享变量或者使用线程通信的方式来达到效果，这样使用起来就比较麻烦。

而自从Java 1.5开始，就提供了Callable和Future，通过它们可以在任务执行完毕之后得到任务执行结果。

[Java并发编程：Callable、Future和FutureTask](https://www.cnblogs.com/dolphin0520/p/3949310.html)

[Spring Boot中使用@Async实现异步调用](http://blog.didispace.com/springbootasync/)

[Spring Boot使用@Async实现异步调用：自定义线程池](http://blog.didispace.com/springbootasync-2/)

[Spring Boot使用@Async实现异步调用：使用Future以及定义超时](http://blog.didispace.com/springbootasync-4/)

* java GC

什么时候？程序员不能具体控制时间，系统在不可预测的时间调用System.gc()函数的时候；当然可以通过调优，用NewRatio控制newObject和oldObject的比例，用MaxTenuringThreshold 控制进入oldObject的次数，使得oldObject 存储空间延迟达到full gc,从而使得计时器引发gc时间延迟OOM的时间延迟，以延长对象生存期。 

对什么东西？超出了作用域或引用计数为空的对象；从gc root开始搜索找不到的对象，而且经过一次标记、清理，仍然没有复活的对象。

做什么？ 删除不使用的对象，回收内存空间；运行默认的finalize,当然程序员想立刻调用就用dipose调用以释放资源如文件句柄，JVM用from survivor、to survivor对它进行标记清理，对象序列化后也可以使它复活。

finalize is a method that the Garbage Collector always calls just before the deletion/destroying the object which is eligible for Garbage Collection, so as to perform clean-up activity. Clean-up activity means closing the resources associated with that object like Database Connection, Network Connection or we can say resource de-allocation. Remember it is not a reserved keyword.
Once finalize method completes immediately Garbage Collector destroy that object.

我们自己的类，要override finalize方法         
         
## 判断计算结果溢出

* 加法 r = x + y
溢出的情况：x和y同号，但是r的符号与他们相反。即，两个正数得到负数，两个负数得到正数。
```java
if(((x ^ r) & （y ^ r)) < 0) {
    throw overflowexpection;
}
```

* 减法 r = x - y
x和y不同号，且r与x不同号。即，正减负得负，负减正得正。
```java
if((x ^ y) & (x ^ r)) < 0) {
    throw expection
}
```

* 乘法 r = x * y
    * int
    两个int的乘法是先转成long，得到一个long的res，然后取后32位得int，返回。
    如果得到的int res与long res不相等则溢出
    `if((int)r != r)`
    
    * long
     等有参数大于32位时，可能出现溢出。这种情况下看得到的结果除y是否还是x，来判断
 ```java
if (((ax | ay) >>> 31 != 0)) {
    // Some bits greater than 2^31 that might cause overflow
    // Check the result using the divide operator
    // and check for the special case of Long.MIN_VALUE * -1
    if (((y != 0) && (r / y != x)) ||
        (x == Long.MIN_VALUE && y == -1)) {
        throw new ArithmeticException("long overflow");
        }
    }
```