---
title: CentOS挂载硬盘操作说明
date: 2017-05-25 16:15:10
tags: [挂载硬盘]
categories: [CentOS]
---
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxpsafg20j20sg0jqgmg.jpg)
## 查看磁盘信息 
```
fdisk -l
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxojv7q0kj20hm0g5gm8.jpg)

从图可以看出 **/dev/sdb**这一块硬盘还没有被挂载。

## 分区操作
### 2T以下容量的硬盘（血的教训）
```
fdisk /dev/sdb
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxonjnh16j20eq03ndfp.jpg)

对**/dev/sdb**这一块硬盘进行分区操作

1. 进入fdisk命令后，输入**m**查看操作提示：
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxoou2af5j20b709h0su.jpg)

2. 输入**n**,进入分区操作
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxopp0oj7j20bd02i0sj.jpg)

3. 选择分区类型,默认为主分区，输入**p**，或者回车；
```    
p: 分为主分区
e: 分为逻辑分区
```
4. 选择分区数，分区数在[1-4]之间，默认为：**1**,回车选择默认；
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxotzy420j208e01amwx.jpg)

5. 选择该分区的起始磁盘数，默认为磁盘最开始的磁盘数，若无相关要求，最好使用默认的起始数。回车选择默认数；
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxoxefllkj20bg00tdfl.jpg)
6. 选择该分区结束磁盘数或者该分区容量大小，默认为磁盘全部容量。回车选择默认数（磁盘最大容量）;
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxoxjveo5j20gu0120si.jpg)
7. 写入分区，输入**w**,等待分区操作结束
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxoyqyb1pj20ad02b0sj.jpg)
8. 分区操作结束。

### 2T以上容量的硬盘

## 格式化分区
1. 查看磁盘信息
```
fdisk -l
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxp0pv7wfj20fq0gp74x.jpg)
从图可以看出，刚才的分区操作成功，新的分区名为**/dev/sdb1**
2. 格式化分区
```
mkfs -t ext3 -c /dev/sdb1
```
上面这条命令执行速度比较慢，如果想象**Windows**的**快速格式化**操作的话，可以使用下面的命令：
```
mkfs ext3  -T largefile /dev/sdb1
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxp6kawtuj20fz0a60sz.jpg)
3. 格式化完毕！

## 挂载分区
1. 挂载分区
```
mount /dev/sdb1 /data
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxp8g8o3lj20ab01tmwz.jpg)
将刚才的分区挂载到根目录下的**data**目录下，
由于**data**目录不存在，所以第一次挂载失败。
创建目录后，再次执行挂载命令，最后挂在成功！
2. 查看系统磁盘信息
```
df -TH
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxpbab8kuj20dm04qweh.jpg)
上图显示，**/dev/sdb1**2T容量成功挂载到**/data**目录上。

到此为此，挂载操作完美成功。但是如果系统重启后，本次挂载的磁盘就会失效。这是因为没有将挂载的信息告诉系统。

## 永久挂载
系统的**/etc/fstab**文件负责记录磁盘挂载信息，所以必须将本次的挂载内容写入到该文件中。
```
磁盘分区    mount目录     文件格式
/dev/sdb1  /data         ext3        defaults    0   0 
```
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxrtxuxg9j20kc05fwei.jpg)

输入**wq**,完成修改保存操作。

### Tips:
fstab中，每条配置信息都分为固定的6个部分
```
[1]:分区路径，或者UUID
[2]:fs_file - 该字段描述希望的文件系统加载的目录点，对于swap设备，该字段为none；对于加载目录名包含空格的情况，用40来表示空格。
[3]:fs_type - 定义了该设备上的文件系统，一般常见的文件类型为ext4 (Linux设备的常用文件类型)、vfat(Windows系统的fat32格式)、NTFS、isoArray600等。在不确定的情况下可以使用auto。
[4]:fs_options - 指定加载该设备的文件系统是需要使用的特定参数选项，多个参数是由逗号分隔开来.对于大多数系统使用"defaults"就可以满足需要。不多说。
[5]:fs_dump  - 该选项被"dump"命令使用来检查一个文件系统应该以多快频率进行转储，若不需要转储就设置该字段为0
[6]:fs_pass - 该字段被fsck命令用来决定在启动时需要被扫描的文件系统的顺序，根文件系统"/"对应该字段的值应该为1，其他文件系统应该为2。若该文件系统无需在启动时扫描则设置该字段为0
```

----------

## 总结
```
1. 分区操作                 fdisk /dev/xxx
2. 格式化分区                mkfs -t ext3 -c /dev/xxx
3. 挂载分区                  mount /dev/xxx /xxx
4. 写入系统文件              vim /etc/fstab
```
