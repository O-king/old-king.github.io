---
title: Zookeeper集群
date: 2017-07-10 14:44:19
tags: [zookeeper]
categories: [zookeeper]
---
<p align='center'>
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhesd5w1p1j20nr03bzkm.jpg)
</p>

Zookeeper集群部署有两种方式：
1. 伪集群部署
2. 真集群部署


## 伪集群部署

这里所谓的“伪”，指的是Zookeeper的服务全部在一台服务器上。一旦服务器出现问题，所有Zookeeper服务全部都会收到影响，脱离了分布式的宗旨。

### 创建一个集群文件夹
```
mkdir /usr/local/zkcluster
```
然后分别创建三个子文件夹，作为Zookeeper的服务路径
> 由于Zookeeper的选举特殊性，这里请创建单数个数的Zookeeper服务

![](http://ww1.sinaimg.cn/large/91ddf859gy1fhesobu63ej209k025glg.jpg)

### 分别复制Zookeeper的文件
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhesq0we1cj20e60bgjry.jpg)
> 3002和3003文件夹里面文件和3001一样

### 修改Zookeeper默认配置
```
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhest8k290j20fb0dkwf0.jpg)

在设置的**dataDir**目录创建**myid**，并填入相应值：
```
echo "1" > ./tmp/zookeeper/data/myid
```

> 保证每个zookeeper的启动端口不一样
> myid文件的值，与zoo.cfg中server.x中x的值对应

### 启动Zookeeper
分别启动zookeeper
```
bin/zkServer.sh start
```

### 查看Zookeeper状态
```
bin/zkServer.sh status
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhesz1kyvbj20ec057mxb.jpg)

> 如果输出内容报错，请检查myid的值和zoo.cfg的server的值是否一样。

## 真集群部署

大致配置和**伪集群部署**一样，只是不需要配置端口不一致，当然也可一致，纯粹看个人喜好。
> 注意各台服务器防火墙是否工作，zookeeper相应端口是否被开放。