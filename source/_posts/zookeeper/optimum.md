---
title: Zookeeper配置优化
date: 2017-07-10 14:44:19
tags: [zookeeper]
categories: [zookeeper]
---
<p align='center'>
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhesd5w1p1j20nr03bzkm.jpg)
</p>

# 配置文件属性说明
1. tickTime：Client-Server通信心跳时间
	Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳。tickTime以毫秒为单位。
	```
	tickTime=2000
	```
2. initLimit：Leader-Follower初始通信时限
	集群中的follower服务器(F)与leader服务器(L)之间初始连接时能容忍的最多心跳数（tickTime的数量）。
	```
	initLimit=5
	```
3. syncLimit：Leader-Follower同步通信时限
	集群中的follower服务器与leader服务器之间请求和应答之间能容忍的最多心跳数（tickTime的数量）。
	```
	syncLimit=2 
	```
4. dataDir：数据文件目录
	Zookeeper保存数据的目录，默认情况下，Zookeeper将写数据的日志文件也保存在这个目录里。
	```
	dataDir=/opt/zookeeper/data
	```
5. clientPort：客户端连接端口
	客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。
	```
	clientPort=2181
	```
6. 服务器名称与地址：集群信息（服务器编号，服务器地址，LF通信端口，选举端口）
这个配置项的书写格式比较特殊，规则如下：
	```
	server.N=YYY:A:B
	server.1=itcast05:2888:3888
	server.2=itcast06:2888:3888
	server.3=itcast07:2888:3888
	```
7. ZK为什么设置为奇数个？
	
	zookeeper有这样一个特性：集群中只要有过半的机器是正常工作的，那么整个集群对外就是可用的。
	
	也就是说如果有2个zookeeper，那么只要有1个死了zookeeper就不能用了，因为1没有过半，所以2个zookeeper的死亡容忍度为0；
	
	同理，要是有3个zookeeper，一个死了，还剩下2个正常的，过半了，所以3个zookeeper的容忍度为1；
	
	同理你多列举几个：2 -> 0; 3 -> 1; 4 - >1; 5 -> 2; 6 -> 2会发现一个规律，2n和2n-1的容忍度是一样的，都是n-1，
	
	所以为了更加高效，何必增加那一个不必要的zookeeper呢。

# 优化操作说明
1. 默认jvm没有配置Xmx、Xms等信息，可以在conf目录下创建java.env文件
```
export JVMFLAGS="-Xms512m -Xmx512m   $JVMFLAGS"
```
2. log4j配置，由于zk是通过nohup启动的，会有一个zookeeper.out日志文件，该文件中记录的是输出到console的日志。
log4j中只要配置输出到console即可，zookeeper.out日积月累会不断变大，要放在容量大的磁盘上。
	```
	zookeeper.root.logger=INFO, CONSOLE
	zookeeper.console.threshold=INFO
	log4j.rootLogger=${zookeeper.root.logger}
	 
	log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
	log4j.appender.CONSOLE.Threshold=${zookeeper.console.threshold}
	log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
	log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L] - %m%n
	```

3. zoo.cfg文件中，dataDir是存放快照数据的，dataLogDir是存放写前日志的。
这两个目录不要配置成一个路径，要配置到不同的磁盘上。
如果磁盘是使用了raid，系统就一块磁盘，那配置到一块磁盘上也可以。
写前日志的部分对写请求的性能影响很大，保证dataLogDir所在磁盘性能良好。

4. zoo.cfg文件中skipACL=yes，忽略ACL验证，可以减少权限验证的相关操作，提升一点性能。

5. zoo.cfg文件中forceSync=no，这个对写请求的性能提升很有帮助，是指每次写请求的数据都要从pagecache中固化到磁盘上，才算是写成功返回。
当写请求数量到达一定程度的时候，后续写请求会等待前面写请求的forceSync操作，造成一定延时。
如果追求低延时的写请求，配置forceSync=no，数据写到pagecache后就返回。但是机器断电的时候，pagecache中的数据有可能丢失。

6. zk的dataDir和dataLogDir路径下，如果没有配置zk自动清理，会不断的新增数据文件。可配置成zk系统自动清理数据文件，但是最求系统最高性能的话，建议人工手动清理文件：
	```
	zkCleanup.sh -n 3  #这样保留三份文件。
	```
7. 查看zk节点状态。重新启动zk节点前后，一定要查看状态
	```
	echo ruok | nc host port
	echo stat | nc host port
	```
8. 配置fsync.warningthresholdms=20，单位是毫秒，在forceSync=yes的时候，如果数据固化到磁盘的操作fsync超过20ms的时候，将会在zookeeper.out中输出一条warn日志。
这个目前zk的3.4.5和3.5版本有bug，在zoo.cfg中配置不生效。我的做法是在conf/java.env中添加java系统属性：
	```
	export JVMFLAGS="-Dfsync.warningthresholdms=20 $JVMFLAGS"
	```