---
title: 不是 LeetCode 的03题
date: 2021-03-19 10:30:40
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 字符串轮转 easy

给定两个字符串 s1 和 s2，请编写代码检查 s2 是否为 s1 旋转而成。

示例

输入：s1 = 'abcd', s2 = 'cdab'
输出：True

输入：s1 = 'abcd', s2 = 'bcad'
输出：False

```javascript
{% raw %}
var isCircle = functionn(s1 , s2){
    if(s1.length !== s2.length){
        return false;
    }
    if(s1 == s2){
        return true;
    }
    s1 += s1;
    return s1.indexOf(s2) > =0;
}
{% endraw %}
```
<p style="text-indent:2em">233333超简单的一题，把 s1 再拼接一下，判断 indexOf 为正就可以了。写总结也要划水，不愧是我了。</p>
<p style="text-indent:2em">下午如果没事应该再来一篇，可能也是划水~</p>
