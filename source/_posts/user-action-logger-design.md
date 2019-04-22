---
title: 访问行为mock
date: 2019/04/22
categories:
    - 技术总结
tags:
    - 开发相关
    - 毕业设计
---
* 设计思路
	* spark streaming作业根据不同的kafka topic进行消费
	* Topics
		* minions_songplay
		* minions_songlike
		* minions_songcomment
 
* 前端埋点设计

```javascript
//播放歌曲记录
    songplay(songInfo){

        openNotification(songInfo);

        axios.get(Utils.defaultURIdefaultURI+"/actionLogger", {
            params:{
                K_topic: 'minions_songplay',
                songId: songInfo.songID
            }
        }).then(function (response) {
            if (response.data === 1){
                message.success('添加歌曲播放记录成功');
                console.log("日志记录成功")
            } else console.log("日志记录错误")
        }).catch(function (error) {
            console.log(error)
        })
    }

    //收藏歌曲
    likesong(songInfo){

        //openNotification(songInfo);

        axios.get(Utils.defaultURIdefaultURI+"/actionLogger", {
            params:{
                K_topic: 'minions_songlike',
                songId: songInfo.songID
            }
        }).then(function (response) {
            if (response.data === 1){
                message.success('收藏成功');
                console.log("日志记录成功")
            } else console.log("日志记录错误")
        }).catch(function (error) {
            console.log(error)
        })
    }

    //评论歌曲
    comment(songInfo){

        //openNotification(songInfo);

        axios.get(Utils.defaultURIdefaultURI+"/actionLogger", {
            params:{
                K_topic: 'minions_songcomment',
                songId: songInfo.songID
            }
        }).then(function (response) {
            if (response.data === 1){
                message.success('评论成功');
                console.log("日志记录成功")
            } else console.log("日志记录错误")
        }).catch(function (error) {
            console.log(error)
        })
    }
```

* 服务端记录

`logger.info("topic:" + k_topic + " songID:" + songID);`

* log4j

```properties
log4j.appender.flume = org.apache.flume.clients.log4jappender.Log4jAppender
log4j.appender.flume.Hostname = hadoop000
log4j.appender.flume.Port = 41415
log4j.appender.flume.UnsafeMode = true
log4j.appender.flume.layout=org.apache.log4j.PatternLayout
log4j.appender.flume.layout.ConversionPattern= %d{yyyy-MM-dd HH:mm:ss,SSS} [%t] [%c] [%p] - %m%n
```
`2019-04-22 13:58:04,419 [http-nio-8080-exec-9] [com.chaoyue.minions.controller.ActionLogController] [INFO] - topic:minions_songlike songID:13`

* spark streaming作业根据不同kafka topic划分




