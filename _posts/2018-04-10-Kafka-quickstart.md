---
title: Kafka quickstart
date: 2018-04-10 11:50:00
categories:
- Kafka
---  

kafka底层有用到zookeeper，之前有看过；  

下载、安装、启动直接参考官网就行，[QuickStart](https://kafka.apache.org/quickstart)  

### 启动zookeeper  

在解压出来的压缩包根路径下执行`bin/zookeeper-server-start.sh config/zookeeper.properties`,即可启动zk；zk和kafka都是java写的，有java环境就行  

执行以上命令，切勿ctrl c 结束zk服务，之后流程需要用到  

### 启动kafka服务  
在开启一个终端，在解压出来的压缩包根路径下执行`bin/zookeeper-server-start.sh config/zookeeper.properties`  
同样不能结束服务，为了执行其他命令，再开启一个终端；也可以nohup 'command' &执行，不过这样看日志方便点  

### 创建主题（创建主题有点慢，这一步发生了什么，以后看看）  
再另一个终端的解压目录下执行`bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test`  
上面的命令指定了副本因子为1，暂时不知道有什么用  
该命令只是创建一个主题，不会一直输出log，所以可以执行`bin/kafka-topics.sh --list --zookeeper localhost:2181`来查看创建的主题；  

### 通过生产者发送消息  
为了直观看到生产者和消费者之间的关系，这里再开启一个终端，执行`bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test`;然后会进入交互环境，每输出的一句话，都会进入一个队列中；  

### 通过消费者消费消息  
再开启一个终端，执行`bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning`，会看到生产者终端输入的内容，实时在生产者输入的内容，在消费者的终端都能立马看到；  

### 设置集群  
以上的几步建立了一个单节点的kafka服务，如果要实现多节点（最好是3个，因为zookeeper需要三个才能选主）；  

集群建立与上面操作类似，不过多了几个broker的配置：  
- 进入解压根目录的bin路径下执行  
```shell
cp server.properties ./server-1.properties
cp server.properties ./server-2.properties
```
然后再修改他们各自id、端口和日志输出log：  
```shell
config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dir=/tmp/kafka-logs-1
 
config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dir=/tmp/kafka-logs-2
```
启动server、server-1、server-2
执行kafka-server-start.sh脚本并传入服务配置文件：`./kafka-server-start.sh ../config/server_name.properties &`
记得先重启zookeeper服务；  

然后添加一个新主题：  
`bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic`  
指定了副本因子为3，正好是broker节点的个数；  
通过`bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic`命令查看刚刚创建的主题；
leader是server.properties的broker.id，随机选举的；  
replicas是broker节点记录的副本，不管他们是否还可用；  
isr是in-sync的副本集合，也就是replicas的子集，这里面的节点都是可用的；  
我本地上的这三项的值和例子里面的就不一样；是随机的


当杀死leader对应broker进程后，在看该topic下的信息，发现leader易主并且replicas没变，而Isr变少了缺少了杀死的brokerId；  
然而该主题还是可以生产和消费，666  


### 将数据导入kafka中  
除了通过生产者将数据写到主题，还可以通过将文本文件导入主题……   


