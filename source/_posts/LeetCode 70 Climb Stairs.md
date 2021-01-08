---
title: LeetCode 70 Climb Stairs
date: 2021-01-08 14:32:27
author: heyXiao
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
summary: LeetCode 题目总结
categories: 技术
tags:
  - JavaScript
  - LeetCode
  - 学习总结
---

## 代码

### [LeetCode 70](https://leetcode.com/problems/climbing-stairs/) ###
    var climbStairs = function(n) {
    	if (n >= 1 && n <= 3) {
    		return n
    	}
    	// 第一种 递归
    	let arr = [0, 1, 2, 3]
    	for (let i = 4; i <= n; i++) {
    		arr.push(arr[i - 1] + arr[i - 2])
    	}
    	return arr[n]

    	// 第二种 斐波那契优化
    	var a = 1,
    		b = 1;
    	while (--n > 0) {
    		b = b + a;
    		a = b - a;
    	}
    	return b;

    	// 第三种 不知道为什么...
    	var sqrt5 = Math.sqrt(5);
    	var fibn = Math.pow((1 + sqrt5) / 2, n + 1) - Math.pow((1 - sqrt5) / 2, n + 1);
    	return (fibn / sqrt5);
    }
    climbStairs(10)
