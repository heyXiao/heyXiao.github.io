---
title: 不是 LeetCode 的 04&05 题
date: 2021-03-22 13:37:56
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 斐波那契 easy

斐波那契数，通常用 F(n) 表示，行程的序列称为斐波那契数列。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。
F(0) = 0, F(1) = 1
F(n) = F(n - 1) + F(n - 2), 其中 n > 1

示例1：
输入：2
输出：1
F(2) = F(1) + F(0) = 1 + 0 = 1

示例2：
输入：3
输出：2
F(3) = F(2) + F(1) = 1 + 1 = 2

示例2：
输入：4
输出：3
F(4) = F(3) + F(2) = 2 + 1 = 3

```javascript
{% raw %}
function fib(n) {
  if(n === 0){
    return 0
  }
  if(n === 2 || n===1){
    return 1;
  }
  let preOne = 1;
  let preTwo = 1;
  let result = 0;
  for(let i = 3; i <= n; i++){
    result = preOne + preTwo;
    preOne = preTwo;
    preTwo = result;
  }
  return result;
}
{% endraw %}
```

### 冒泡排序 easy

比较相邻的两个元素，如果前一个比后一个大，则交换位置。
比较完第一轮的时候，最后一个元素是最大的元素。
这时候最后一个元素是最大的，所以最后一个元素就不需要参与比较大小。

示例1：
输入：[20, 18, 27, 19, 35]
输出：[18, 19, 20, 27, 35]

```javascript
{% raw %}
function popSort(arr) {
  var len = arr.length;
  for (var i = 0; i < len - 1; i++) {
    for (var j = 0; j < len - 1 - i; j++) {
         // 相邻元素两两对比，元素交换，大的元素交换到后面(相反就改成小于号)
        if (arr[j] > arr[j + 1]) {
            var temp = arr[j];
            arr[j] = arr[j+1];
            arr[j+1] = temp;
        }
    }
  }
  return arr;
}
//举个数组
myArr = [20,18,27,19,35];
//使用函数
popSort(myArr)
{% endraw %}
```

### 题外话
<p style="text-indent:2em">今天忽然很想写这两题，想起了大学第一次用 C++ 编译这两个函数的时候，我喜欢它们。</p>
<p style="text-indent:2em"></p>

