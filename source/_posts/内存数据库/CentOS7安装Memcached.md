---
title: CentOS7安装Memcached
date: 2017-06-02 11:02:56
tags: [Memcache]
categories: [内存数据库]
---

<p align="center">
![](http://memcached.org/images/memcached_banner75.jpg)
</p>

1. #查找Memcached
```
yum search memcached
```
2. #安装Memcached
```
yum -y install memcached
```
3. #验证安装
```
memcached -h
```
4. #查看配置文件
```
cat /etc/sysconfig/memcached
```
可以根据情况修改相关配置参数：
```
PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="64"
OPTIONS=""
```
5. #memcached命令
    1. 启动命令
    ```
    systemctl start memcache
    ```
    2. 停止命令
    ```
    systemctl stop memcache
    ```
    3. 查看状态命令
    ```
    systemctl status memcache
    ```
    4. 开机自动启动命令
    ```
    systemctl enable memcached.service
    ```

