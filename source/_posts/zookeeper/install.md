---
title: zookeeper安装
date: 2017-07-10 13:46:35
tags: [zookeeper]
categories: [zookeeper]
---
<p align='center'>
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhesd5w1p1j20nr03bzkm.jpg)
</p>

## 下载Zookeeper
```
wget http://mirror.bit.edu.cn/apache/zookeeper/ 
```

## 解压
```
tar zxvf zookeeper-3.4.10.tar.gz -C /usr/local 
```

## 配置文件
```
cp zoo_sample.cfg zoo.cfg		# 拷贝配置文件
```

## 启动Zookeeper
```
bin/zkServer.sh start
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1fheqzbr1qvj20cl01p3yd.jpg)

## 关闭Zookeeper
```
bin/zkServer.sh stop
```