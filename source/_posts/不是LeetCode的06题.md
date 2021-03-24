---
title: 不是 LeetCode 的06题
date: 2021-03-24 15:00:47
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 删除字符串中的所有相邻重复项 medium

给定一个字符串 s，"k 倍重复项删除操作" 将会从 s 中选择 k 个相邻且相等的字母，并使被删除的字符串的左侧和右侧连在一起。
你需要对 s 重复进行无限次这样的删除操作，直到无法继续为止。
在执行完所有删除操作后，返回最终得到的字符串。

示例

输入：s = 'abcd'，k = 2
输出：'abcd'
解释：没有要删除的内容

输入：s = 'deeedbbcccbdaa'，k = 3
输出：'aa'
解释：先删除 'eee' 和 'ccc'，得到 'ddbbbdaa'
再删除 'bbb'，得到 'dddaa'
最后删除 'ddd'，得到 'aa'

输入：s = 'pbbcggttciiippooaais'，k = 2
输出：'ps'
解释：先删除 'bb'、'gg'、'tt'、'ii'、'pp'、'oo'、'aa'，得到 'pcciis'
再删除 'cc'、'ii'，得到 'ps'

```javascript
{% raw %}
var delMult = function(s, k) {
    let arr = s.split('');
    let fn = function (arr, k) {
        if (arr.length < k) {
            return arr.join('');
        }
        // 这里还是拿 s = 'abcd'，k = 2 来举例，只需判断 4 - 2 + 1 = 3 次
        // 从 s[i] = 'a' 与 s[i + j] = 'b' 对比 
        for (let i = 0; i < arr.length - k + 1; i++) {
            let flag = true;
            for (let j = 1; j < k; j++) {
                if (arr[i + j] !== arr[i]) {
                    flag = false;
                    break;
                }
            }
            // 如果是从第 i 个起，重复 k 个。删除这 k 个字符，递归。
            if (flag) {
                arr.splice(i, k);
                fn(arr, k);
            }
        }
        return arr.join('');
    };
    return fn(arr, k);
};
{% endraw %}
```
<p style="text-indent:2em">本来是从上午 09:52:40 开始写的，中间忙了一会，到下午 3 点才写完.....说实话还是想了一下</p>
