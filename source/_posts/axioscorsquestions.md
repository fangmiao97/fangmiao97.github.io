---
title: 关于axios的CORS问题的解决
date: 2019/3/18
categories:
    - 技术总结
tags: 
    - axios
    - CORS
---
## Axios基础
* 参考网站
	* [中文文档](https://www.jianshu.com/p/7a9fbcbb1114)
	* [使用说明](https://www.kancloud.cn/yunye/axios/234845)
* 上下文知识
	* [Promise in ES6](http://es6.ruanyifeng.com/#docs/promise)
	* [浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html) 同源政策的目的，是为了保证用户信息的安全，防止恶意的网站窃取数据
	* [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html) 实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信

## 问题描述
Access to XMLHttpRequest at 'http://localhost:8080/hello' from origin 'http://localhost:3000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.

## 解决方法
* [Spring利用@CrossOrigin注解 实现 支持CORS跨域请求](https://my.oschina.net/hccake/blog/886606) 在controller类头部加入注释

```java
@CrossOrigin(origins = "*", maxAge = 3600)
```