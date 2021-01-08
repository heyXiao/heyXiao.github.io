---
title: LeetCode 64 Minimum Path Sum
date: 2021-01-08 11:50:22
author: heyXiao
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
summary: LeetCode 题目总结
categories: 技术
tags:
  - JavaScript
  - LeetCode
  - 学习总结
---
## 代码 ##

### [LeetCode 64](https://leetcode.com/problems/minimum-path-sum/) ###
	var minPathSum = function(grid) {
		// 如果阵列长度为0
		if (!grid.length) {
			return 0
		}
		// grid 为
		// [1, 3, 1],
		// [1, 5, 6],
		// [4, 2, 1],
	
		// 初始化一个二维数组,存储路径和
		let dp = [[]];
	
		// dp[0] 赋值 grid[0] 路径和 (第一行)
		// grid[0] == [1, 3, 1];
		// dp[0][0] = 1, dp[0][1] = 1 + 3, dp[0][2] = 1 + 3 + 1;
		// dp[0] == [1, 4, 5];
	
		let sum = 0;
		for (let i = 0; i < grid[0].length; i++) {
			sum += grid[0][i]
			dp[0].push(sum)
		}
	
		// dp[i][0] 赋值 grid[i][0] 路径和 (第一列)
		// 赋值过后 dp 为 
		// [1, 4, 5],
		// [2, A, B],
		// [6, C, D]
		// 偏右下的位置为当前终点的最小路径和 比如说 A 点两个值 1 + 4 与 1 + 2, 取最小值3
		let sum2 = dp[0][0];
		for (let i = 1; i < grid.length; i++) {
			sum2 += grid[i][0];
			dp[i] = [];
			dp[i].push(sum2);
		}
		// dp				//grid
		// [1, 4, 5],		// [1, 3, 1],
		// [2, A, B],		// [1, 5, 6],
		// [6, C, D]		// [4, 2, 1]
		// 计算 A, B, C, D 四个点的路径和
		for (let i = 1; i < grid.length; i++) {
			for (let j = 1; j < grid[0].length; j++) {
				// 比较右和下两个方向路径和的值,取最小
				// 例 i == 1, j == 1, dp[0][1] == 4, dp[1][0] == 2, 
				// 则 dp[1][1] = dp[1][0] + grid[1][1] == 2 + 5 == 7
				dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]
			}
		}
		// 最终结果
		// [1, 4, 5],
		// [2, 7, 11],
		// [6, 8, 9]
		// 取右下角值 结果为 9
		return dp[dp.length - 1][dp[0].length - 1]
	}
	minPathSum([
		[1, 3, 1],
		[1, 5, 6],
		[4, 2, 1],
	])
### 做什么 ###
一开始只是写LeetCode，后来有了总结学习的想法，慢慢做吧，题目不按顺序，不刻意最优解。