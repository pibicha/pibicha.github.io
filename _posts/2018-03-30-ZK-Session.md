---
title: ZK-Session
date: 2018-03-30 23:30:09
categories:
- 分布式
tags:
- Zookeeper
description:
image: https://picsum.photos/id/37/2000/1200
image-sm: https://picsum.photos/id/37/500/300
---  

**原文的client在以下都被我作为session来解释**

### 官网对ZK Sessions的解释  
![image](http://ww2.sinaimg.cn/large/0060lm7Tly1fpumw0kmz9j311t0ehwgc.jpg
)  

```text
To create a client session the application code must provide a connection string containing a comma separated list of host:port pairs, each corresponding to a ZooKeeper server (e.g. "127.0.0.1:4545" or "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002"). The ZooKeeper client library will pick an arbitrary server and try to connect to it. If this connection fails, or if the client becomes disconnected from the server for any reason, the client will automatically try the next server in the list, until a connection is (re-)established.
```
建立一次ZK的Session，需要提供一个连接字符串，其内容是Zookeeper集群中的主机IP和端口，多个服务用逗号分隔，比如"127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002"(这里隐含了zk的集群个数必须大于2并且是奇数个)；ZK的客户端将会选举一个主服务来连接此次会话，如果失败或者断开连接，ZK客户端会自动尝试选举一个主服务，直到连接或重连成功。  

### 3.2.0 版本  
基于之前所说的连接字符，新增了一个“chroot”参数，使用该参数会为连接字符增加一个后缀,比如："127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002/app/a";则ZK的客户端会在各自服务器相对于/的目录下建立该节点；  

### SessionId  
当集群下的ZK服务启动时，代表其中一个客户端已经被推举出来，ZK会创建一个Zk会话，其会话ID以64位表示并赋值给这个客户端；  
当这个客户端与其他客户端连接时，会将sessionId作为连接所需数据传送给对方，出于安全因素考虑会将密码作为连接所需数据的一部分也发给对方，并且接收到的数据的服务可以验证它；  

### timeout  
与其他ZK服务连接的超时时间，最小是tickTime的2倍，最大20倍；当然也是可以修改的  

### 加入会话成为子节点
当一个会话是由不同IP上服务组成时，会去寻找session创建时的连接字符中的机器（上文有提到），当会话中至少一个节点服务在重连时，session的状态会过度为已连接或过期（如果重连超过了timeout时间）； 不建议创建一个新的session会话（由于ZK的底层实现会新生成一个ZooKee.class），ZK的底层会为程序员自动处理重连。之所以由ZK底层强制实现，是为了避免已以往出现的“惊群”现象

### Session过期  
Session过期不是由会话决定，而是由各自的节点服务决定；当节点没有在指定的时间内收到会话的心跳时，会话终结时刻，节点会删除此session的`/`目录下的所有临时节点，并立即通知所有连接的ZK节点所发生的变化；  
当session处于断开连接的时候，是无法通知到会话中各个子节点的；此时就需要`Watcher`  

对于Session状态迁移的过程Example:  
```
'connected' : session is established and client is communicating with cluster (client/server communication is operating properly)

.... client is partitioned from the cluster

'disconnected' : client has lost connectivity with the cluster

.... time elapses, after 'timeout' period the cluster expires the session, nothing is seen by client as it is disconnected from cluster

.... time elapses, the client regains network level connectivity with the cluster

'expired' : eventually the client reconnects to the cluster, it is then notified of the expiration
```

---
3.2.0 新增一个`SessionMovedException`异常，出现在一个zk服务发送给session消息但是超时了，随后创建了一个新的session，但是新的session又收到了之前节点发送的数据  

