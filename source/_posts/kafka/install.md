---
title: Kafka安装
date: 2017-07-10 15:14:52
tags: [kafka]
categories: [kafka]
---
<p  align='center'>
![](http://ww1.sinaimg.cn/large/91ddf859gy1fheu4yvaqvj20r308374g.jpg)
</p>
## 下载
```
wget http://mirror.bit.edu.cn/apache/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz
```

## 解压
```
tar zxvf kafka_2.11-0.11.0.0.tgz -C /usr/local/
```

## 配置Zookeeper
```
vim config/server.properties
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1fheu8w245kj20ep03nq2w.jpg)

## 启动服务
```
bin/kafka-server-start.sh config/server.properties
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1fheualpfihj210h04wdgh.jpg)

## 后台启动
```
bin/kafka-server-start.sh config/server.properties 1>/dev/null 2>&1 &
```

## 创建Topic
```
bin/kafka-topics.sh --create --zookeeper localhost:3001 --replication-factor 1 --partitions 1 --topic 
```

可以通过**list**命令查看创建的topic：
```
bin/kafka-topics.sh --list --zookeeper localhost:3001
```
除了手动创建topic，还可以配置broker让它自动创建topic。

## 发送消息
运行producer并在控制台中输一些消息，这些消息将被发送到服务端：
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

## 接收消息
Kafka也有一个命令行consumer可以读取消息并输出到标准输出：
```
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
```