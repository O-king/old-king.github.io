---
title: Resilio Sync共享工具
date: 2017-02-06 14:15:02
tags: [工具]
---

当自己需要多设备同步或与同事需要频繁共享文件时，QQ 或微信的文件传输相对是不够用的，使用 Dropbox 或百度云这样的网盘服务，来保持文件始终最新会是更省事的方法。

不过，通过这些网盘传文件，基本都需要把文件上传到服务器，由此会产生这几个问题：

* 对网络环境依赖较高。你需要先把文件上传到服务器，之后再由服务器分发到其它设备上，即使在局域网下，上传下载速度仍取决于网速；
* 容量和传输速度受限。网盘都会有空间大小限制，如果达到上限，只能付费扩容；国内的百度云更是会对传输速度进行限制；
* 影响文档结构。这些服务会在设备上创建一个文件夹，且只会对该文件夹内的内容进行同步，如果你习惯整理文档，这种同步方式势必会影响你的文档结构；
* 安全问题。这也是很多人最关心的一点，国内网盘隐私保护没保障，还有随时关停的风险；国外大厂虽值得信赖，但因访问困难，也不适合所有人。

[**Resilio Sync**](https://www.resilio.com)，原名：BitTorrent Sync，
采用了不一样的解决方法：不需要把文件上传到云端服务器，而是通过 P2P 的方式，直接将文件从你的设备传到对方设备上，它不限速、不限文件大小、不需要注册账号。

资源网站：[http://wherebt.com/](http://wherebt.com/)

# 使用介绍

## 共享文件
1. 添加分享文件夹
  ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgsbkwu13j20l8077js2)
  **分享文件夹有三种**：
  * 标准文件夹
  * 高级文件夹
  * 加密文件夹

  ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgrygzsz6j20le0htq4q)

> 经过测试，只有加密文件夹可以通过秘钥分享，其他只能通过连接或者二维码的方式分享。

2. 设置共享文件夹属性
 * 普通文件夹属性：

      + 链接分享
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgrmc303bj20le0htdh6)
      + 二维码分享
        ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgsejx39aj20le0htwfp)
 * 加密文件夹属性：
    ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgsi2w7mfj20le0htwfy)

## 下载文件
1. 点击“输入秘钥或链接”
  ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgrygzsz6j20le0htq4q)
2. 输入相应秘钥或链接
  ![](http://ww1.sinaimg.cn/large/91ddf859gy1fcgsko7ehtj20le0ht3zi)


​	
