---
title: CentOS7下安装Mysql
date: 2017-04-19 10:25:45
tags: [软件安装]
---
# Mysql数据库软件安装与配置

## centos7下快速安装mysql

 CentOS 7的yum源中貌似没有正常安装MySQL时的mysql-sever文件，需要去官网上下载

 ```shell
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum install mysql-community-server
 ```

​

## 重启mysql服务

```shell
service mysqld restart
```

​

## 设置root用户密码

​

* 连接mysql(初次安装mysql，root默认无密码)

```shell
mysql -uroot
```

* 设置root用户密码为root

```shell
set password for ‘root’@‘localhost’ = password('root');
```

* 远程连接授权

```shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
FLUSH   PRIVILEGES;
```