---
title: 不是 LeetCode 的07题
date: 2021-03-31 16:53:26
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 最后一块石头的重量 easy

有一堆石头，每块石头的重量都是正整数。
每一回合，从中选出两块最重的石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，并且 x <= y。那么粉碎的可能结果如下
如果 x == y，那么两块石头都会被完全粉碎；
如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y - x。
最后，最多只会剩下一块石头。返回此石头的重量。如果没有石头剩下，就返回 0。

提示：
1 <= stones.length <= 30
1 <= stones[i] <= 1000

```javascript
{% raw %}
var lastStone = function(stones) {
    let fn = (arr) => {
        if(arr.length === 1){
            return arr[0]
        }
        if(arr.length === 2){
            return arr[0] === arr[1] ? 0 : fn([Math.abs(arr[0] - arr[1])]);
        }
        arr.sotr((a,b) => {
            return b - a;
        })
        if(arr[0] === arr[1]){
            return fn(arr.slice(2));
        }else{
            let a = arr.slice(2);
            a.push(Math.abs(arr[0] - arr[1]));
            return fn(a);
        }
    };
    return fn(stones)
};
console.log(lastStone([2, 7, 4, 1, 8, 1]));
{% endraw %}
```
<p style="text-indent:2em">本来今天不想写的，很烦，3月的最后一天。</p>
