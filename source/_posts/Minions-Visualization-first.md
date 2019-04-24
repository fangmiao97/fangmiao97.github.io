---
title: 【毕业设计Minions一期】可视化部分展示
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
    
![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxf2zx0cj21hc0u07h0.jpg)

* 当前PV（page view）值统计
    * 显示周同比、日环比和7日PV均值

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxf2u6f5j20gg062t9h.jpg)

* 7日PV走势
    * PV值在7日周维度上的拓展

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxf2u8fvj20fk04cgm3.jpg)

* 今日实时PV走势
    * 每隔30分钟统计前30分钟窗口内PV数据
    * 是 当前PV统计 在时间维度下的细化

![](https://wx3.sinaimg.cn/mw1024/6a49516fly1g2dxf2w57fj20fh0anmyl.jpg)

* 各类目访问统计
    * 音乐网站首页下属6个子类目
    * 统计今日当前时间下各类目访问情况
    * 是 当前PV值统计 在类目维度下的细化

![](https://wx3.sinaimg.cn/mw1024/6a49516fly1g2dxer2jnaj20fi0bpmz4.jpg)

* 各类目访问趋势
    * 今日实时PV走势 在类目维度的细化
    * 亦是 各类目访问统计 在时间维度上的拓展

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxer2k6fj20fg0eitcb.jpg)

* Top来源网站统计
    * 访问日志其他信息统计

![](https://wx3.sinaimg.cn/mw1024/6a49516fly1g2dxer06woj20f70b6wfk.jpg)

---

* 网站历史数据Review
    * 可选择日期查看该日相应指标数据
    * 部分指标与今日数据相同
    * 增加时间维度对比统计展示

![](https://wx4.sinaimg.cn/mw1024/6a49516fly1g2dxer7xxnj21hc0u0n8c.jpg)

* 选择日期模块

![](https://wx4.sinaimg.cn/mw1024/6a49516fly1g2dxer2rfgj20910f4jt9.jpg)

* 30天月PV数据统计

![](https://wx3.sinaimg.cn/mw1024/6a49516fly1g2dxeqyzmfj20ff07nq4i.jpg)

---
* 音乐网站用户行为分析模拟
    * 设计有音乐播放模块，模拟用户音乐播放、收藏、评论行为

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxer21m0j21hc0u0qm6.jpg)

* 音乐模块
    * 按钮从左到右，模拟音乐播放、收藏和评论
    * 音乐播放显示全局提示，模拟播放60秒

![](https://wx1.sinaimg.cn/mw1024/6a49516fly1g2dxeqw5o6j20700c4ab6.jpg)

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxer1oocj219t0ik7ht.jpg)

![](https://wx4.sinaimg.cn/mw1024/6a49516fly1g2dxe3s5u7j204z02a749.jpg)

![](https://wx3.sinaimg.cn/mw1024/6a49516fly1g2dxe3sn9zj204x023q2w.jpg)

* 今日歌曲播放数据
    * 从记录的日志中，通过不同维度展示用户操作歌曲的行为

![](https://wx4.sinaimg.cn/mw1024/6a49516fly1g2dxe3y2z4j21hc0u0al0.jpg)

* 今日歌曲播放数据一览
    * 根据播放数量排列

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxe3wuz4j20fi0l8di5.jpg)

* 近一小时播放TOP歌曲
    * 每隔十分钟统计前一个小时窗口期内的播放数据
    * 是今日歌曲播放总览的子集

![](https://wx2.sinaimg.cn/mw1024/6a49516fly1g2dxe3umj4j20f30iv75k.jpg)

* 播放音乐类型统计
    * 根据播放歌曲的类型进行统计
    * 展示各个类型的流行程度

![](https://wx4.sinaimg.cn/mw1024/6a49516fly1g2dxe3vbvgj20f40el3zz.jpg)

* 今日TOP10歌曲标签云

![](https://wx4.sinaimg.cn/mw1024/6a49516fly1g2dxe3u8lsj20f308uq43.jpg)

* 其他歌曲信息维度的统计
    * 收藏量统计
    * 评论量统计
    * 不放在一起是因为可能有的歌有收藏没有评论

![](https://wx1.sinaimg.cn/mw1024/6a49516fly1g2dxe3uzugj20f80mo76a.jpg)

---

## TODO...
* 推荐？协同过滤？