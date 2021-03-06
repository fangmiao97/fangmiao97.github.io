---
title: Minions-frontend 框架技术结构
date: 2019/3/21
categories:
    - 毕业设计
---
## 前端技术选型
* react 16.8.4
* Ant Design ([Pro v1](https://v1.pro.ant.design/docs/getting-started-cn)) 3.15.0
* XMLRequest:axios 0.18.0
* 路由: react-router-dom
* [精选组件](https://ant.design/docs/react/recommendation-cn)

## 目录结构
* components 放置个性化组件
    * PageView 展示PV相关数据组件
* contents 主体部分抽象布局
    * Overview 今日数据概览
    * HistoryView 历史数据查询
* pages 页面（目前为单页面）

## React-Router
* react-router-dom 是对 react-router 在浏览器开发环境下的封装，所以没有本质差别

## ant design pro
* 需要引入css文件
`import 'ant-design-pro/dist/ant-design-pro.css';`

## [BizChart](https://bizcharts.net/index)
* 商业场景下的数据可视化

## react相关
* [dangerouslySetInnerHTML, 让React正常显示你的html代码](http://www.cnblogs.com/xianyulaodi/p/5038258.html)
* [时间显示组件](https://github.com/pvoznyuk/react-live-clock)
    * 获取今天的时间
    
    `const today = moment().format('YYYYMMDD');`
    
* react setState是异步调用的
    * 使用props和componentWillReceiveProps(nextProps)来解决
    * [见topicid的变换](https://github.com/fangmiao97/MessageWiKiPro-frontend/blob/master/src/TopicInfo.js)
    * 在minions中的历史数据页面，需要根据选择的不同日期显示相关数据。日期选择为父组件中的state.date改变，并将state.date传给child组件。child接收后通过componentWillReceiveProps来进行更新

* [flex布局](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

* RestFul API

* 组件定时刷新
```javascript
componentDidMount() {
        this.timer = setInterval(
            () => {
                //更新组件state...
            },
            60000
        );
    }
```

* 组件动态渲染
```javascript
    //父组件
    this.state = {
                songsInfoList:[]
            }
    
            render() {
                    let songInfoList = this.state.songsInfoList;
                    return (
                        <div>
                            {
                                songInfoList.map((item, index) => {
                                    return <MockSongComponent key={index} songInfo={item}/>
                                })
                            }
                        </div>
                    );
                }
                
    //子组件
    this.state = {
                songInfo: this.props.songInfo
            }
            
    render() {
    
            let songInfo = this.state.songInfo;
    
            return(
                <div>
                  {songInfo.name}
                  {songInfo.coverUrl}
                  ......
                </div>
            );
    }
```