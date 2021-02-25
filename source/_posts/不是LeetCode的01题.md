---
title: 不是 LeetCode 的01题
date: 2021-02-25 17:04:27
author: heyXiao
summary: 非 LeetCode 题目的技术总结
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
categories: 技术
tags:
  - JavaScript
  - 学习总结
---

### 判定字符是否唯一 easy

实现一个算法，确定一个字符串 s 的所有字符是否全都不同。

示例 1：
输入: s = "leetcode"
输出: false
示例 2：
输入: s = "abc"
输出: true
限制：
0 <= len(s) <= 100
如果你不使用额外的数据结构，会很加分。

    var isUnique = function (astr) {
      // 哈希表解法
      if (!astr.length) return true;
      let hash = {};
      for (let i = 0; i < astr.length; i++) {
        // 如果 hash 中已有 则 false
        if (hash[astr[i]]) {
          return false;
        } else {
        // 否则存储
          hash[astr[i]] = true;
        }
      }
      // 如果一直没 false
      return true;
    };

果然很easy....趁着今天有点闲，再水一博。不过很快就要忙了。