---
title: ZK-一致性协议
date: 2018-04-21 09:30:09
categories:
- 分布式
tags:
- Zookeeper
description:
image: https://picsum.photos/id/99/2000/1200
image-sm: https://picsum.photos/id/99/500/300
---  

[参考](https://cwiki.apache.org/confluence/display/ZOOKEEPER/Zab+vs.+Paxos)
### zookeeper与paxos的相似点：
- leader给予follwer提供参考值
- leader提交建议之前会等待follwer的通知  
- 每个请求建议（应该是zxid），会包含一个纪元号，和Paxos的投票选举类似  
但是其主要差距在于，zab主机宕机备份恢复为主，而不是状态机副本为主。  



### primay-backup和state machine replication的区别  
state machine replication能保证每次接收到的有序请求，都能得到正确的处理
primary-backup系统比如zookeeper，副本可以按增量式状态排序（zxid前32位表示所属会议，后32位是递增数字），
是由主要副本（应该是leader）生成发送给其他follwer的，state machine replication不一样，如果主机宕了，客户端就找不到可以处理请求的机器了。


### zookeeper专门的一致性算法——zab  

zab是zookeeper atomic broadcast 的缩写；在以上两种概念之下，zab对于state machine repliacation的具体实现方式：  
对象设计：
- leader：  
整个zk会议中，负责更新状态的服务；兼职发起投票
- leaner：  
    - follower：处理客户端的请求，并可以参与投票和选举
    - observer：处理客户端的请求，但是不可以参与投票和选举
    
zab的两种模式：  
广播：  
这种状态是zk运行的正常状态，可以处理正常请求  
恢复：  
由于leader宕了，需要重新选择一个leader出来  

- 选主流程
模式方式是fast-paxos，首先会获取所有follwer的zxid（默认首推发其选举者的），然后将zxid最大者的，像其他follwer发起投票，如果该zxid获得超过半数以上的支持，
会将其设置为leader，其他follower则更新自己zxid前32位为新leader的zxid前32位。







