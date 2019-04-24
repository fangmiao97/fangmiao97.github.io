---
title: 【毕业设计Minions】一期可视化展示
date: 2019/04/24
categories: 
    - 毕业设计
---
## 题目
* 基于Spark Streaming的网站实时流量分析

## 技术栈
* 前端框架
    * react
    * 组件
        * ant-design
        * ant-design Pro
        * BizCharts

* 后端框架
    * springboot

* 持久化
    * HBase
    * MySQL

## 可视化内容概述
* 可视化展示用户访问日志数据和用户行为日志，本设计具体化为"音乐网站"日志分析

## 细化展示
---
* 今日访问日志数据指标
    * 指标数据模块展示部分，从左向右、从上到下指标维度不断细化

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dv7yug8kj31hc0u0n4g.jpg)

* 当前PV（page view）值统计
    * 显示周同比、日环比和7日PV均值

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dvyp6z7qj30gg062aae.jpg)

* 7日PV走势
    * PV值在7日周维度上的拓展

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dw0x5b7gj30fk04cdfy.jpg)

* 今日实时PV走势
    * 每隔30分钟统计前30分钟窗口内PV数据
    * 是 当前PV统计 在时间维度下的细化

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dw31imuoj30fh0anmy6.jpg)

* 各类目访问统计
    * 音乐网站首页下属6个子类目
    * 统计今日当前时间下各类目访问情况
    * 是 当前PV值统计 在类目维度下的细化

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dw4k248yj30fi0bpt9u.jpg)

* 各类目访问趋势
    * 今日实时PV走势 在类目维度的细化
    * 亦是 各类目访问统计 在时间维度上的拓展

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dw92ym3zj30fg0eimzj.jpg)

* Top来源网站统计
    * 访问日志其他信息统计

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dway8ks0j30f70b6mxl.jpg)

---

* 网站历史数据Review
    * 可选择日期查看该日相应指标数据
    * 部分指标与今日数据相同
    * 增加时间维度对比统计展示

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwcj46wrj31hc0u0afs.jpg)

* 选择日期模块

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwi49zfmj30910f4my6.jpg)

* 30天月PV数据统计

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwitkniwj30ff07n74t.jpg)
---
* 音乐网站用户行为分析模拟
    * 设计有音乐播放模块，模拟用户音乐播放、收藏、评论行为

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwjmq23jj31hc0u0nfa.jpg)

* 音乐模块
    * 按钮从左到右，模拟音乐播放、收藏和评论
    * 音乐播放显示全局提示，模拟播放60秒

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwqmbdv0j30700c4gmv.jpg)

![](https://ws3.sinaimg.cn/large/005BYqpggy1g2dwu7r9mcj319t0ikdv7.jpg)

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwuk5p65j304z02awee.jpg)

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwutopabj304x023t8m.jpg)

* 今日歌曲播放数据
    * 从记录的日志中，通过不同维度展示用户操作歌曲的行为

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dwv953gfj31hc0u0gra.jpg)

* 今日歌曲播放数据一览
    * 根据播放数量排列

![](https://ws3.sinaimg.cn/large/005BYqpggy1g2dwwurftlj30fi0l8ab8.jpg)

* 近一小时播放TOP歌曲
    * 每隔十分钟统计前一个小时窗口期内的播放数据
    * 是今日歌曲播放总览的子集

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dx03096ij30f30iv3z6.jpg)

* 播放音乐类型统计
    * 根据播放歌曲的类型进行统计
    * 展示各个类型的流行程度

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dx2edn4aj30f40elt9g.jpg)

* 今日TOP10歌曲标签云

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dx41c10fj30f308uq3i.jpg)

* 其他歌曲信息维度的统计
    * 收藏量统计
    * 评论量统计
    * 不放在一起是因为可能有的歌有收藏没有评论

![](https://ws3.sinaimg.cn/large/005BYqpgly1g2dx62jsxhj30f80mogmj.jpg)