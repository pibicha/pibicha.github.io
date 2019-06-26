---
title: Hadoop quick start
date: 2018-04-10 23:14:00
categories:
- Hadoop
---  

### docker安装Hadoop镜像
最近看分布式的框架，先横向都了解一下……  
Hadoop最近公司项目中有用到，不过是运维小哥搭的，自己回来闲着看看；结果官网搭建太麻烦了，找到一个好用的docker镜像，直接google kiwenlau就好了。。  

根据大神提供的镜像，在我128G乞丐版Mac也能跑Hadoop了。。。  
只不过进入Hadoop网页管理地址不是虚拟机的IP，我本地直接用localhost+端口才能进入……  

### 目录结构
在进入搭建好的虚拟机后，可以看到hdfs目录，该目录是存储数据的地方，其下有两个目录，一个namenode，一个datanode；前者是存储元数据的，后者是存储一系列block的；  
namenode和resourceManager是master管理，slave中相对的是datanode和nodemanager；node是处理数据输入输出，而manager负责任务CPU和磁盘内存的调度，，  

明天在看。。。