---
title: 不是 LeetCode 的08题
date: 2021-04-12 15:44:13
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 乘积最大子序列 medium

给定一个整数数组 nums，找出一个序列中乘积最大的连续子序列（该序列至少包含一个数）
示例 1：
输入：[2, 3, -2, 4]
输出：6 = 2 * 3
示例 2：
输入：[-2, 0, -1]
输出：0


```javascript
{% raw %}
var max = function(nums){
    if(!nums.length){
        return 0;
    }
    let dp = [nums[0]]; // [2]
    let curMax = nums[0];// 2
    let curMin = nums[0];// 2
    // [2, 3, -2, 4, 1]
    for(let i = 1; i < nums.length; i++){
        let temp = curMax;
        curMax = Math.max(Math.max(nums[i] * curMax, nums[i] * curMin), nums[i]);
        curMin = Math.min(Math.min(nums[i] * temp, nums[i] * curMin),nums[i]);
        dp[i] = Math.max(curMax, dp[i - 1]);
        // i = 1, nums[1] = 3, temp = 2
        // curMax = Math.max(Math.max(3 * 2, 3 * 2), 3) = 6
        // curMin = Math.min(Math.min(3 * 2, 3 * 2), 3) = 3
        // dp[1] = Math.max(6, 2) = 6

        // i = 2, nums[2] = -2, temp = 6, curMax = 6, curMin = 3
        // curMax = Math.max(Math.max(-2 * 6, -2 * 3), -2) = -2
        // curMin = Math.min(Math.min(-2 * 6, -2 * 3), -2) = -12
        // dp[2] = Math.max(-2, 6) = 6

        // i = 3, nums[3] = 4, temp = -2, curMax = -2, curMin = -12
        // curMax = Math.max(Math.max(4 * -2, 4 * -12), 4) = 4
        // curMin = Math.min(Math.min(4 * -2, 4 * -12), 4) = -48
        // dp[3] = Math.max(4, 6) = 6
    }
    return dp[dp.length - 1]
}
console.log(max([2, 3, -2, 4, 1]))
{% endraw %}
```
<p style="text-indent:2em">还没搞完，忙..</p>
