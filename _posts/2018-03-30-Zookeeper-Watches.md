---
title: ZK-Session
date: 2018-03-30 23:30:09
categories:
- 分布式
tags:
- Zookeeper
---  

在ZK的读操作上：`getData()`,`getChildern()`,`exists()`上，可以自己选择是否设置watch；
有三种设置：
### 一次性触发  
`getData('path',true)`，当path下的数据被删除或更新时，session会收到事件；path下的数据再次更新时，不会发送  
### 发送给session  
这种方式是异步的，也就是观察的节点发生了改变，发送给session的事件可能已经到达，但是节点本身的改变还未完成；对此Zookeeper提供了顺序的保证：session在收到watch事件之前都不会观察到节点的改变；这样就保证各个节点无论由于网络延时或是其他因素，最终看到数据的一致性  
### 设置需要观察的数据  
除了ZK本身在读操作上可以设置watch以外，可以在set方法上也可以设置watch
