---
title: DOCKER上传镜像到私服
date: 2018-05-18 10:30:09
categories:
- DOCKER
tags:
- 容器
description:
image: https://picsum.photos/2000/1200?image=1003
image-sm: https://picsum.photos/2000/1200?image=1003
---  


# 1 配置`DOCKER`私服信息  
修改`/etc/docker/daemon.json
`文件(没有该文件的话创建一个)  
```
{
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ],
  "insecure-registries": ["gcr.io", "k8s.gcr.io", "idockerhub.xx.com","dockerhub.xx.com"]
}

```

然后重启`DOCKER`服务  

```
# 若没有权限执行，命令前缀使用sudo
systemctl daemon-reload
systemctl restart docker
```

# 2 上传本地镜像到`DOCKER`私服  

## 2.1 登陆  
`docker login dockerhub.xx.com`

## 2.2 将本地镜像打上对应标签  

```
# docker tag <local> <remote> 

docker tag python:base2.7 dockerhub.xx.com/algorithm-service-platform/python:base2.7
```

 
remote的命名规则： 域名/项目名/镜像名

## 2.3 push 到私服
```
docker push dockerhub.xx.com/algorithm-service-platform/python:base2.7
```