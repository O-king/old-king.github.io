---
title: Git操作说明---回滚文件
date: 2017-05-18 18:31:23
categories: [Git]
tags: [Git]
---

![](http://ww1.sinaimg.cn/large/91ddf859gy1ffwerjv3qyj20hy0b4jrr.jpg)

## 本文将记录Git使用过程中回滚文件的操作说明。

## 情况假设
1. 改完代码匆忙提交,上线发现有问题,怎么办? 赶紧回滚.
2. 改完代码测试也没有问题,但是上线发现你的修改影响了之前运行正常的代码报错,必须回滚.


**这些开发中很常见的问题,所以git的取消提交,回退甚至返回上一版本都是特别重要的.**

## 解决方案

1. **没有commit到本地仓库**
		git checkout fileName
		
	命令执行过后，文件会恢复到上次commit后的内容

	**小结：**
	- git checkout file1 （回滚单个文件）
	- git checkout file1 file2 ... fileN （一次回滚多个文件，中间用空格隔开即可）
	- git checkout . （直接回滚当前目录一下的所有working tree内的修改，会递归扫描当前目录下的所有子目录）

2. **已经commit到本地仓库**
	1. 查看提交记录
		![](http://ww1.sinaimg.cn/large/91ddf859gy1ffpbxiuph0j20b102vq2w.jpg)
	2. 执行回滚操作
		- 命令说明
			```
			git reset [--soft | --mixed | --hard]
			```
		1. \--soft		 
				保留源码,只回退到commit 信息到某个版本.不涉及index的回退,如果还需要提交,直接commit即可.
			![](http://ww1.sinaimg.cn/large/91ddf859gy1ffpc6lxk5yj20gz0az75b.jpg)
	
		 **本地仓库版本已经回退到选择的版本号,该文件已经处于暂存区，只需要执行commit命令，就可提交到工作区**
		
		2. \-- mixed
				会保留源码,只是将git commit和index 信息回退到了某个版本.

			```
			git reset 默认是 --mixed 模式 
			git reset --mixed  等价于  git reset
			```
			![](http://ww1.sinaimg.cn/large/91ddf859gy1ffpci3ohgoj20hi04iq2y.jpg)
	
	 	**本地仓库版本已经回退到选择的版本号,该文件不在暂存区，还需要执行add、commit命令，就可提交到工作区**

		3. \--hard		 
				源码也会回退到某个版本,commit和index 都回回退到某个版本.(注意,这种方式是改变本地代码仓库源码)
			![](http://ww1.sinaimg.cn/large/91ddf859gy1ffpcod2n0bj209k03q744.jpg)

		 **本地仓库版本已经回退到选择的版本号,该文件同时也恢复到该版本号的内容**

	**小结：**	
	- HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
	- 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
	- 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。		

3. **已经push到远程仓库**
	```
	git revert HEAD                     //撤销最近一次提交
	git revert HEAD~1                   //撤销上上次的提交，注意：数字从0开始
	git revert 0ffaacc                  //撤销0ffaacc这次提交
	```
	**小结：**
	- revert 是撤销一次提交，所以后面的commit id是你需要回滚到的版本的前一次提交		
	- 使用revert HEAD是撤销最近的一次提交，如果你最近一次提交是用revert命令产生的，那么你再执行一次，就相当于撤销了上次的撤销操作，换句话说，你连续执行两次revert HEAD命令，就跟没执行是一样的		
	- 使用revert HEAD~1 表示撤销最近2次提交，这个数字是从0开始的，如果你之前撤销过产生了commi id，那么也会计算在内的。	
	- 如果使用 revert 撤销的不是最近一次提交，那么一定会有代码冲突，需要你合并代码，合并代码只需要把当前的代码全部去掉，保留之前版本的代码就可以了.

## 总结
```	
git checkout file  			//回退没有commit的文件，多个文件以空格隔开，本目录用.表示,会递归回退本级目录以及所有子目录
git reset commitId			//回退工作区文件，版本号会回退
git revert commitId			//回退远程仓库，会产生新的版本号
```
  			
				



