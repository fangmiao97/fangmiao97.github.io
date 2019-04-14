---
title: Minions初始化手册✋
date: 2019/03/27
---

* kill所有进程

* 启动zooKeeper

`zookeeper>bin ./zkServer.sh start`

* 启动Kafka（后台）

`kafka> bin/kafka-server-start.sh -daemon $KAFKA_HOME/config/server.properties`

* 启动两个flume
	* 对接Python产生的伪日志
	* 对接前端埋点的数据

```
//页面浏览日志 agent
flume-ng agent \
--name exec-memory-kafka \
--conf $FLUME_HOME/conf \
--conf-file /home/hadoop/data/project/streaming_project2.conf \
-Dflume.root.logger=INFO,console

//页面行为日志 agent
flume-ng agent \
--name agent1 \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/streaming2.conf \
-Dflume.root.logger=INFO,console
```

* 启动Hadoop
	* DataNode
	* SecondaryNameNode
	* NameNode 

```
hadoop/sbin> ./start-dfs.sh

//单独启动命令：
./hadoop-daemon.sh start secondarynamenode
```

* 启动HBase
	* HMaster
	* HRegionServer 

`bin> ./start-hbase.sh`

```
./hbase shell

list
scan 'table_name'
desc 'table_name'
```

* 启动spark作业
	* MyStreamingApp : 统计访问量
	* KafKaStreamingApp : 处理前端埋点数据（hello_ladygaga_topic)

* 启动Springboot后端和react前端