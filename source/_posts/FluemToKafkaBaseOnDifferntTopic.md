---
title: Flume使用interceptors向不同的Kafka Consumers发送基于Topic维度的信息
date: 2019/3/16
categories:
    - 技术总结
tags:
    - 开发相关
    - Flume
---
# 需求
* 后端接口通过log4j转发给flume的Event是直接发给avro source，之后要交给kafka sink供不同的应用处理。如果对于不同的topic都用一个agent处理感觉略尴尬（没有尝试），所以用一个avro source进行接收，用flume的interceptor进行按topic分流。

# 方案流程
* 前端
    * 向后端上传数据时，加入topic字段
  
```javascript
axios.get("url/logtest", {
            params:{
                K_topic: 'specialTopic',
                contents: 'contents'
            }
```

* 后端
    * 在log4j的logger.info中加入topic字段

```java
logger.info("topic:" + k_topic + " " + "contents:"+ contents);
```

* flume配置（部分）

```
#source interceptor
agent1.sources.avro-source.interceptors=i1
agent1.sources.avro-source.interceptors.i1.type=regex_extractor
agent1.sources.avro-source.interceptors.i1.regex=topic:(.*?) contents:(.*?)
agent1.sources.avro-source.interceptors.i1.serializers=s1 s2
#agent1.sources.avro-source.interceptors.i1.serializers.s1.type=default
agent1.sources.avro-source.interceptors.i1.serializers.s1.name=topic
#agent1.sources.avro-source.interceptors.i1.serializers.s2.type=default
agent1.sources.avro-source.interceptors.i1.serializers.s2.name=contents

#define sink
agent1.sinks.kafka-sink.type=org.apache.flume.sink.kafka.KafkaSink
agent1.sinks.kafka-sink.topic = %{topic}
```

* 结果
不同的kafka consumer能够接收不同topic的埋点数据

# 参考文档
* [regex-extractor-interceptor正则表达式interceptor](http://flume.apache.org/releases/content/1.9.0/FlumeUserGuide.html#regex-extractor-interceptor)
* [interceptor](http://lxw1234.com/archives/2015/11/543.htm)