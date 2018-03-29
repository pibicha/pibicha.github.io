---
title: Zookeeper QS
date: 2018-03-29 20:13:00
categories:
- 分布式
tags:
- IT
---

# Zookeeper QuickStart
本想看看kafaka的，没想到官网上来就让下载Zookeeper，所以先看看ZK  
### ZK安装 参考[官网](https://zookeeper.apache.org/doc/current/index.html)  
1. pre-requisite：对于Mac来说不支持Native Client(没有对C语言提供类似Java的客户端来调用ZK)和Contrib功能，先不管。。  
2. 下载并解压zookeeper-3.4.10.tar，按照官网标准的文档或者参考解压后的conf/zoo_sample.cfg文件即可：  
```shell  
tickTime=2000  
dataDir=/var/lib/zookeeper  
clientPort=2181
```  
tickTime是心跳时间，最小会话时间是他的两倍（不知道翻译错没.）  
dataDir是数据存放目录，是快照和数据存放的位置，最好不要放\tmp下，Centos7会自动清理\tmp下的内容   
3. 回到解压zk的目录下执行`bin/zkServer.sh start`即可启动ZK（ZK的log使用的是log4j）  
可以自己设置输出格式和目录：  
```shell    
 java -cp zookeeper.jar:lib/slf4j-api-1.6.1.jar:lib/slf4j-log4j12-1.6.1.jar:lib/log4j-1.2.15.jar:conf org.apache.zookeeper.server.PurgeTxnLog <dataDir> <snapDir> -n <count>  
```
通过指定参数运行jar的方式，来设置数据存储位置和日志输出位置  
4. 通过`bin/zkCli.sh -server 127.0.0.1:2181`连接到zk；  

- 在连接上zk后，直接通过`ls \path`命令可以看到有多少个zookeeper的节点；然后可以通过`create \path 'data'`命令创建一个节点，该节点可以携带一些信息即`data`；比如`create /test_node my_data`  
；然后再通过get /test_node可以得到该节点的信息（可以根据`path`名管理一些同类型的变量：比如longCfg下的节点存储的是long类型的变量，ingCfg下的是int类型的变量；或者是Aservice下的变量放到/Aservice下，Bservice下的变量放到/Bservice下）  
- [待续](https://zookeeper.apache.org/doc/current/zookeeperStarted.html#sc_Prerequisites)
