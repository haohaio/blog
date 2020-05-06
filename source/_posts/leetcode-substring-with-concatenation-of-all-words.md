---
title: "[JavaScript LeetCode]30. 串联所有单词的子串"
entitle: leetcode-substring-with-concatenation-of-all-words
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 数据结构
comments: true
date: 2020-04-30 11:25:21
timestamp:
tags:
  - LeetCode
keywords:
description:
photos:
  - /img/tags/leetcode.png
---

[原题链接](https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/)

> 滑动窗口第 2 题

给定一个字符串 s 和一些长度相同的单词 words。找出 s 中恰好可以由 words 中所有单词串联形成的子串的起始位置。

注意子串要与 words 中的单词完全匹配，中间不能有其他字符，但不需要考虑 words 中单词串联的顺序。

示例：

```code
输入：s = "barfoothefoobarman", words = ["foo","bar"]
输出：[0,9]
解释：从索引 0 和 9 开始的子串分别是 "barfoo" 和 "foobar"。输出的顺序不重要, [9,0] 也是有效答案。

输入：s = "wordgoodgoodgoodbestword", words = ["word","good","best","word"]
输出：[]
```

### 暴力解法

解题思路：

- 用 `map` 来存储 `words`，把 `words` 中的单词作为 `key`，单词出现的次数作为 `value`
- 循环字符串 `s`，然后每次从字符串 `s` 中截取一段长度为 `words` 单词总长的字符串，然后按照单个 `words` 单词的长度，对其进行拆分成单词
- 使用拆分后的单词去 `map` 中查询，如果存在，则将其 `value - 1`，否则表明当前字符串不符合要求
- 内层循环结束后，如果 `map` 所有的 `value` 都为 `0`，则表明当前子字符串符合要求，将其起始索引放入结果集中
- 最后返回结果集

```js
var findSubstring = function (s, words) {
  if (!s || !words || !words.length) return [];
  let wordLen = words[0].length;
  let allWordsLen = wordLen * words.length;
  let len = s.length;
  let map = {};
  let ans = [];

  for (let word of words) {
    map[word] ? map[word]++ : (map[word] = 1);
  }

  for (let i = 0; i <= len - allWordsLen; i++) {
    let vm = { ...map };
    for (let j = i; j <= i + allWordsLen - wordLen; j += wordLen) {
      // 将字符串切割成长度为 wordLen 的子字符串
      let w = s.slice(j, j + wordLen);
      if (vm[w]) {
        vm[w]--;
      } else {
        break;
      }
    }

    if (Object.values(vm).every((item) => item === 0)) {
      ans.push(i);
    }
  }

  return ans;
};
```

### 滑动窗口解法

```js
var findSubstring = function (s, words) {
  if (!s || !words || !words.length) return [];
  let wordLen = words[0].length;
  let allWordsLen = wordLen * words.length;
  let len = s.length;
  let windows = {}; // 存储滑动窗口收集到的目标 words
  let needs = {}; // 存储目标 words

  for (let word of words) {
    needs[word] ? needs[word]++ : (needs[word] = 1);
  }

  let needsKeyLen = Object.keys(needs).length;
  let left = 0;
  let right = 0;
  let match = 0; // 若 windows[word] === needs[word]， 则  match + 1
  let ans = [];

  // 只需遍历一个 word 的长度，就可取得切割成一个 word 长度的子字符串的所有情况
  for (let i = 0; i < wordLen; i++) {
    windows = {};
    right = left = i;
    match = 0;

    while (right <= len - wordLen) {
      // 滑动窗口右边界以一个 word 的长度为单位进行递增，并截取子字符串
      let w1 = s.slice(right, right + wordLen);
      right += wordLen;

      // 若切割得到的子字符串不是目标 word, 直接让左边界等于右边界，重新滑动窗口
      if (!needs[w1]) {
        windows = {};
        left = right;
        match = 0;
        continue;
      }

      windows[w1] ? windows[w1]++ : (windows[w1] = 1);

      if (windows[w1] === needs[w1]) match++;

      while (match === needsKeyLen) {
        // 若滑动窗口长度等于目标 words 的所有长度， 则将左边界添加到结果中
        if (right - left === allWordsLen) {
          ans.push(left);
        }

        let w2 = s.slice(left, left + wordLen);
        left += wordLen;
        if (needs[w2]) {
          windows[w2]--;
          if (windows[w2] < needs[w2]) match--;
        }
      }
    }
  }

  return ans;
};
```
