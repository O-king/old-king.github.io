---
title: Redis安装
date: 2017-07-10 11:23:47
tags: [Redis]
categories: [Redis]
---
<p align='center'>
![](https://redis.io/images/redis-white.png)
</p>

## 下载Redis-3.2.9
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhen4vklu1j20re0ehmyz.jpg)
```
wget -c http://download.redis.io/releases/redis-3.2.9.tar.gz
```
## 解压
```
tar xzf redis-3.2.9.tar.gz -C /usr/local
```
## 安装
```
cd /usr/local/redis-3.2.9 	#进入redis安装目录
make 						#编译
make install 				#安装
```

## 启动
```
/usr/local/redis-3.2.9/src/redis-server	# 已默认配置启动
```

如果出现一下内容，说明启动成功：
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhen8mi51xj20u808d74b.jpg)

## 后台启动

默认的方式不是后台启动，如果这个控制台关闭的话，Redis服务也会随之关闭。
1. 修改配置
```
vim /usr/local/redis-3.2.9/redis.conf
```
将**daemonize**的值改为**yes**,
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhenbizd5cj20g002smx1.jpg)

2. 重新启动
```
/usr/local/redis-3.2.9/src/redis-server	/usr/local/redis-3.2.9/redis.conf	# 已特定配置启动
```

## 停止Redis实例
```
/usr/local/redis-3.2.9/src/redis-cli shutdown   # Redis自带命令
pkill redis-server								# 杀死Redis进程
```

## Redis开机启动
```
vim /etc/rc.local
```
加入以下内容：
```
/usr/local/redis-3.2.9/src/redis-server	/usr/local/redis-3.2.9/redis.conf
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1fhenhb3tfrj20eu06kt8t.jpg)


## Redis配置文件说明
```
daemonize：如需要在后台运行，把该项的值改为yes
pdifile：把pid文件放在/var/run/redis.pid，可以配置到其他地址
bind：指定redis只接收来自该IP的请求，如果不设置，那么将处理所有请求，在生产环节中最好设置该项
port：监听端口，默认为6379
timeout：设置客户端连接时的超时时间，单位为秒
loglevel：等级分为4级，debug，revbose，notice和warning。生产环境下一般开启notice
logfile：配置log文件地址，默认使用标准输出，即打印在命令行终端的端口上
database：设置数据库的个数，默认使用的数据库是0
save：设置redis进行数据库镜像的频率
rdbcompression：在进行镜像备份时，是否进行压缩
dbfilename：镜像备份文件的文件名
dir：数据库镜像备份的文件放置的路径
slaveof：设置该数据库为其他数据库的从数据库
masterauth：当主数据库连接需要密码验证时，在这里设定
requirepass：设置客户端连接后进行任何其他指定前需要使用的密码
maxclients：限制同时连接的客户端数量
maxmemory：设置redis能够使用的最大内存
appendonly：开启appendonly模式后，redis会把每一次所接收到的写操作都追加到appendonly.aof文件中，当redis重新启动时，会从该文件恢复出之前的状态
appendfsync：设置appendonly.aof文件进行同步的频率
vm_enabled：是否开启虚拟内存支持
vm_swap_file：设置虚拟内存的交换文件的路径
vm_max_momery：设置开启虚拟内存后，redis将使用的最大物理内存的大小，默认为0
vm_page_size：设置虚拟内存页的大小
vm_pages：设置交换文件的总的page数量
vm_max_thrrads：设置vm IO同时使用的线程数量
```