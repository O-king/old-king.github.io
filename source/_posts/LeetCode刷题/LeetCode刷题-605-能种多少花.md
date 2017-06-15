---
title: LeetCode刷题---605.能种多少花
date: 2017-06-15 18:23:53
tags: [LeetCode]
categories: [LeetCode]
---

<p align="center" style="background:black;border-radius:10px">
![](https://leetcode.com/static/images/LeetCode_nav.png)
</p>

# [原题](https://leetcode.com/problems/can-place-flowers/#/description)
```
Suppose you have a long flowerbed in which some of the plots are planted and some are not. However, flowers cannot be planted in adjacent plots - they would compete for water and both would die.

Given a flowerbed (represented as an array containing 0 and 1, where 0 means empty and 1 means not empty), and a number n, return if n new flowers can be planted in it without violating the no-adjacent-flowers rule.

Example 1:
Input: flowerbed = [1,0,0,0,1], n = 1
Output: True
Example 2:
Input: flowerbed = [1,0,0,0,1], n = 2
Output: False
Note:
1. The input array won't violate no-adjacent-flowers rule.
2. The input array size is in the range of [1, 20000].
3. n is a non-negative integer which won't exceed the input array size.
```

## 翻译
```
在一个花坛里面种花，不能在相邻的地方种花，
也就是说：种一朵花，要间隔一块地。

用数组来代表花坛[1,0,0,0,1],
1表示该处种花，
0表示该处空出。

提示：
1. 输入的数组是符合种花原则的（花不相邻）
2. 数组大小在1-20000之间
3. n不能超过数组大小
```

# 解题

## 思路
1. 查看花坛相邻的空地有多少，至少需要三块空地相连
2. 三块相邻空地才能种一朵花，因为这三块头部和尾部都已经种了花了，所以只能中间一块空地才能种上
3. 3-----1
4. 4-----1
5. 5-----2
6. n-----(n-1)/2

## 编码
```java
public boolean canPlaceFlowers(int[] flowerbed, int n) {
		//判断相邻多少块空地
        int count = 0;
		//能种多少花
        int result = 0;
		//遍历数组
        for (int i = 0; i < flowerbed.length; i++) {
            if (flowerbed[i] == 0) {
                count++;
            } else {
                result += (count-1 ) / 2;
                count = 0;
            }
        }

        if (count != 0){
			 result += count / 2;
		}

        return result >= n;
    }
```