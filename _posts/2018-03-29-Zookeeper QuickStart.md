---
title: Zookeeper QS
date: 2018-03-29 20:13:00
categories:
- 分布式
tags:
- IT
---

本想看看kafaka的，没想到官网上来就让下载Zookeeper，所以先看看ZK  
### ZK安装 ，参考[官网](https://zookeeper.apache.org/doc/current/index.html)  
1. pre-requisite：对于Mac来说不支持Native Client(没有对C语言提供类似Java的客户端来调用ZK)和Contrib功能，先不管。。  
2. 下载并解压zookeeper-3.4.10.tar；启动ZK服务需要创建`conf/zoo.cfg`文件，按照官网标准的文档或者参考解压后的conf/zoo_sample.cfg文件即可：  
```shell  
tickTime=2000  
dataDir=/var/lib/zookeeper  
clientPort=2181
```  
tickTime是心跳时间，会话最短超时时间是他的两倍（不知道翻译错没...）  
dataDir是数据存放目录，是快照和数据存放的位置，最好不要放\tmp下，Centos7会自动清理\tmp下的内容  
clientPort是启动端口；
3. 回到解压zk的目录下执行`bin/zkServer.sh start`即可启动ZK（ZK的log使用的是log4j,而且Zk是Apache用Java实现的），所以可以通过`java`命令设置输出日志格式和输出目录：  
```shell    
 java -cp zookeeper.jar:lib/slf4j-api-1.6.1.jar:lib/slf4j-log4j12-1.6.1.jar:lib/log4j-1.2.15.jar:conf org.apache.zookeeper.server.PurgeTxnLog <dataDir> <snapDir> -n <count>  
```
通过指定参数运行jar的方式，来设置数据存储位置和日志输出位置  
### ZK服务启动  
通过`bin/zkCli.sh -server 127.0.0.1:2181`连接到zk；  

- 在连接上zk后，直接通过`ls \path`命令可以看到有多少个zookeeper的节点；然后可以通过`create \path 'data'`命令创建一个节点，该节点可以携带一些信息即`data`；比如`create /test_node my_data`  
；然后再通过get /test_node可以得到该节点的信息（可以根据`path`名管理一些同类型的变量：比如longCfg下的节点存储的是long类型的变量，ingCfg下的是int类型的变量；或者是Aservice下的变量放到/Aservice下，Bservice下的变量放到/Bservice下）  

### ZNode信息  
ZNode节点下，会有一堆信息，[官网的个一个例子](https://zookeeper.apache.org/doc/current/zookeeperProgrammers.html)：
```shell

my_data

#The zxid of the change that caused this znode to be created.
cZxid = 5  
ctime = Fri Jun 05 13:57:06 PDT 2009

#The zxid of the change that last modified this znode.
mZxid = 5
mtime = Fri Jun 05 13:57:06 PDT 2009

#The zxid of the change that last modified children of this znode.
pZxid = 5

cversion = 0

dataVersion = 0

aclVersion = 0

#The session id of the owner of this znode if the znode is an ephemeral node. If it is not an ephemeral node, it will be zero.
ephemeralOwner = 0

dataLength = 7

#The number of children of this znode.
numChildren = 0
```  
比较关注的四个属性是：czxid、mzxid、pzxid和ephemeralOwner；前三个与zxid有关，而对于zxid的解释:`Every change to the ZooKeeper state receives a stamp in the form of a zxid (ZooKeeper Transaction Id). This exposes the total ordering of all changes to ZooKeeper. Each change will have a unique zxid and if zxid1 is smaller than zxid2 then zxid1 happened before zxid2.`  
;zookeeper服务状态更新时会收到一个zookeeper定义的事务ID(ZooKeeper Transaction Id)——即zxid；  
而ephemeralOwner只有当该节点是临时节点，才代表此次会话（ZooKeeper Sessions）的ID、否则为0  

### QS总结  
zookeeper的节点操作正如官网所说，类似于文件操作；每个节点可以看作一层文件目录，每层目录可以携带一些信息数据，数据大小可以通过get命令看到其dataLength。这里只是在单个服务器上体验了一把，先留下对zxid和ZooKeeper Sessions的疑问，下一篇在看。