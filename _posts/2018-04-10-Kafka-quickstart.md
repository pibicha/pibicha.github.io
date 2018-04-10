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
该命令只是创建一个主题，不会一直输出log，所以可以执行`bin/kafka-topics.sh --list --zookeeper localhost:2181`来查看创建的主题；  

### 通过生产者发送消息  
为了直观看到生产者和消费者之间的关系，这里再开启一个终端，执行`bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test`;然后会进入交互环境，每输出的一句话，都会进入一个队列中；  

### 通过消费者消费消息  
再开启一个终端，执行`bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning`，会看到生产者终端输入的内容，实时在生产者输入的内容，在消费者的终端都能立马看到；  

### 设置集群  
内存紧张，没搞虚拟机实验。。
