---
title: 系统设计学习 System design
date: 2020/01/15
categories:
    - 系统设计
tags:
    - 系统设计
---

## 分布式ID生成器

* 应用场景

生成用户ID、微博ID、帖子ID、聊天信息ID等等

* 需求

    * 生成的ID作为数据库的主键，unique, auto increasing, primary key
    * clustered index, as short as possible, 64bits prefer.
    * sortable by time, but not absolute, which means in the distributed system, we can never get strictly time-sorted ID.

* 解决方案

    * UUID
        * 一类算法的统称，使用一串可以分成不同部分的标志符来表示资源，标准的是128位。MongoDB中使用的是12字节。
        * 标志符由几部分组成：时间戳timestamp、机器ID、进程ID、自增计数器
        * 每台机器可以独立产生ID，理论上保证不会重复，所以天然是
分布式的，缺点是生成的ID太长，不仅占用内存，而且索引查询效率低

    * 多台mysql服务器
        * 不同的机器从不同的初始值开始增加数字，比方说两台话，就是一台奇数一台偶数。
        * 在mysql机器前用round robin load-balancer循环负载均衡器来选择mysql服务器。
        * 实现简单，但是不是严格递增的。

    * Twitter(snowflake)和ins的方案
        * 用一个长串标识符表示ID，标识符由不同的部分组成，比如时间戳、机器id和序列号。


## 短网址TinyURL生成

* 场景

微博Twitter有字数限制，需要将长的URL转成短网址。另外通过短网址的转换，记录一些信息，比方说时间发布的用户等等（电商）。

* 需求

    * 尽可能的短，使用长度为7，62进制的字符组成（大小写字母＋数字）
    * 一对多映射，即一个长网址可以对应多个短网址，因为短网址还有另一个功能就是数据统计，一个短网址ID之下可以挂多种信息，比如生成该网站的用户名、时间等等信息，用于以后数据分析。

* 解决方案
    * 计算短网址？不是用hash算法，使用分布式ID生成器，也就是上面一个设计
    * 存储，使用关系型数据库mysql或者分布式KV数据库。
    * 如果用了301，Google，百度等搜索引擎，搜索的时候会直接展示真实地址，那我们就无法统计到短地址被点击的次数了，也无法收集用户的Cookie, User Agent 等信息，这些信息可以用来做很多有意思的大数据分析，也是短网址服务商的主要盈利来源。
    * 使用302临时重定向，新浪微博用的就是302
    * 预防攻击，限制IP的单日请求总数，另外使用一个缓存服务器，存储长网址-》短网址的数据，这样如果同一个长网址请求很多次短网址，就根据LRU机制来返回短网址ID，不至于消耗很多短网址ID。

##
