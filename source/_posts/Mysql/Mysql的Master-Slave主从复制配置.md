---
title: Mysql的Master-Slave主从复制配置
date: 2017-05-24 09:53:04
tags: [Mysql]
categories: [Mysql]
---

<p align="center">
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzvdjnuohj20d206yjrc.jpg)
</p>
今天来说一下Mysql的主从复制配置方面的事情。

手上有两台服务器，信息如下：

名称         |       IP          |备注
        -   |   -               |
**Master**  |   192.168.0.1     | 主服务器
**Slave**   |   192.168.0.2     | 节点服务器

## Master-Slave主从配置

1. **主服务器配置**
编辑Mysql配置文件：
** vi /usr/my.cnf**
```shell
[mysqld]
log-bin=mysql-bin   //[必须]启用二进制日志
server-id=1      //[必须]服务器唯一ID，默认是1，一般取IP最后一段
```
2. **从服务器配置**
编辑Mysql配置文件：
** vi /usr/my.cnf**
```shell
[mysqld]
log-bin=mysql-bin   //[不是必须]启用二进制日志
server-id=2      //[必须]服务器唯一ID，默认是1，一般取IP最后一段
```
3. **重启两台Mysql服务**
```
/etc/init.d/mysql restart
```
4. **在主服务器上建立账户并授权Slave**
    1. 连接服务器
        ```shell
        mysql -uroot -p默认密码
        ```
    2. 建立账户
        ```shell
        GRANT REPLICATION SLAVE ON *.* to 'repl_user'@'%' identified by 'repl_user';
        ```
    3. 锁表操作
        ```
        flush tables with read lock;
        ```
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzqtpjznuj207q019we9.jpg)
    4. 查询Master的状态
        ```shell
        show master status;
        ```
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzqqj39gdj20i4038glg.jpg)
5. **配置从服务器**
    1. 配置Master信息
    
            change master to
                master_host='192.168.0.1',          //Master服务器IP
                master_user='repl_user',            //Master连接用户名，必须具备Slave权限
                master_password='repl_user',        //Master连接用户密码，必须具备Slave权限
                master_log_file='mysql-bin.000010', //数据库二进制日志文件名，Master服务器 Show master status命令输出的文件名
                master_log_pos=0;                   //数据库二进制日志文件开始位置

    2. 检查Slave状态        
            show slave status \G;
         
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzr3bwk7lj20f006474a.jpg) 

        **注意**： 这样标识从服务器配置成功                
            Slave_IO_Running:Yes
            Slave_SQL_Running:Yes           

6. **解除Master的锁表状态**
        UNLOCK TABLES;
    
## Master-Master主主配置

上面已经完成了Master_Slave的配置操作，主主（双主）就是将上面的操作反过来重新操作一次。
```
    第一次的主机变为从机，
    第一次的从机变为主机。
```

Mysql配置文件如下：
```
server_id = 2                                    //服务器id，在集群中此id不可重复
log-bin= mysql-bin                               //开启二进制日志文件
binlog_format = mixed                            //日志记录方式
expire_logs_days        = 7                      //binlog过期清理时间
max_binlog_size         = 100m                   //binlog每个日志文件大小    
binlog_cache_size       = 4m                     //binlog缓存大小   
max_binlog_cache_size   = 512m                   //最大binlog缓存大小  
log-slave-updates=on                             //该服务器既作为从库，又作为主库的时候，必须开启，否则它的从库无法获得二进制日志    

replicate_wild_ignore_table=mysql.%              //忽略mysql库的sql语句

auto-increment-offset=2                          //自增主键从1开始计数 
auto-increment-increment=2                       //自增主键每次加2
```

## 总结
1. Mysql的主从复制，必须开启二进制日志记录功能，也就是在配置文件中加入：
```
server_id = 2                                    //服务器id，在集群中此id不可重复
log-bin= mysql-bin                               //开启二进制日志文件
```
2. 主从复制失败
    1. 查看Master服务器状态
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzqqj39gdj20i4038glg.jpg)  
      
    2. 查看Slave服务器状态
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzr3bwk7lj20f006474a.jpg)     
        两个的二进制日志文件要相同，如果不同    
        1. 重置Master服务器二进制日志文件
                reset master; 
        2. 配置Slave服务器的Master连接信息
                change master to
                    master_host='192.168.0.1',          //Master服务器IP
                    master_user='repl_user',            //Master连接用户名，必须具备Slave权限
                    master_password='repl_user',        //Master连接用户密码，必须具备Slave权限
                    master_log_file='mysql-bin.000010', //数据库二进制日志文件名，Master服务器 Show master status命令输出的文件名
                    master_log_pos=0;                   //数据库二进制日志文件开始位置            

3. 主主复制，如果采用的是主键自增模式话，注意两台服务器的自增策略不能相同，以免同步的时候发生主键重复
   修改Mysql配置文件：
    ```
    auto-increment-offset=2                          //自增主键从1开始计数 
    auto-increment-increment=2                       //自增主键每次加2
    ```
