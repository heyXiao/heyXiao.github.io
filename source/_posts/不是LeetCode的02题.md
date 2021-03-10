---
title: 不是 LeetCode 的02题
date: 2021-03-10 12:33:40
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 零矩阵 medium

实现一个算法，若 M * N 矩阵中某个元素为 0，则将其所在的行列清零

示例 1：
输入:
[
[1,1,1],
[1,0,1],
[1,1,1]
]
输出:
[
[1,0,1],
[0,0,0],
[1,0,1]
]

示例 2：
输入:
[
[0,1,2,3],
[4,5,6,7],
[8,9,1,0]
]
输出:
[
[0,0,0,0],
[0,5,6,0],
[0,0,0,0]
]

```javascript
{% raw %}
var setZero = function(matrix) {
    // 标记出来所有需要清零的行和列
    let row = new Set();
    let col = new Set();
    // 循环矩阵 存储行列
    for (let i = 0; i < matrix.length; i++) {
        for (let j = 0; j < matrix[0].length; j++) {
            if (matrix[i][j] == 0) {
                row.add(i);
                col.add(j);
            }
        }
    }
    console.log(row, col);
    // 行清零
    // row[i] 为行数，matrix[row[i]] 为所在行，matrix[row[i]][j] 为所在行的每一个数
    // matrix[0].length 为矩阵宽度
    row = Array.from(row);
    for (let i = 0; i < row.length; i++) {
        for (let j = 0; j < matrix[0].length; j++) {
            matrix[row[i]][j] = 0;
        }
    }
    // 列清零
    // col[i] 为列数，[col[i]] 为所在列，matrix[j][col[i]] 为所在列的每一个数
    col = Array.from(col);
    for (let i = 0; i < col.length; i++) {
        for (let j = 0; j < matrix.length; j++) {
            matrix[j][col[i]] = 0;
        }
    }
    console.log(matrix);
    return matrix;
};
{% endraw %}
```
<p style="text-indent:2em">最近有在思考职业发展，感觉现在的公司业务面窄，技术栈单一，对个人综合提升没有多大帮助。打算跳一个中大厂吧，要认真努力，看看自己目前能达到的最高的位置。</p>
<p style="text-indent:2em">今天没有修改的话，下午应该还会做一篇 easy。</p>
