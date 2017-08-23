---
title: CodeWars题目练习 - 6 (字符串查重)

date: 2017-8-23

tag: [javascript,codewars,练题]
---

## 题目描述

假设有一个字符串 'abcdea'

一次遍历每个元素，如果该个元素在字符串中出现了多次，将其替换为 ‘)'

如果只出现了一次，替换为'('

注意在查找是忽略大小写

## 常规解答

```javascript
function duplicateEncode(word){
    // ...
    function times(item, arr)
    {
      if (typeof item == 'string') item = item.toLowerCase();
      var time = 0;
      for (let i = 0; i < arr.length; i++)
      {
        if (arr[i].toLowerCase() == item) time++;
      }
      return time;
    }
    var arr = word.split('')
    return arr.map(function(item){
      return times(item, arr) > 1 ? ')' : '('
    }).join('')   
}
```

- 设计一个函数，用来计算一个元素在数组中出现了几次
- 计算字符串中每个元素出现了多少次，如果大于1，返回 ‘)’,反之'('

## 最优解答

```javascript
#1
function duplicateEncode(word){
  return word
    .toLowerCase()
    .split('')
    .map( function (a, i, w) {
      return w.indexOf(a) == w.lastIndexOf(a) ? '(' : ')'
    })
    .join('');
}

#2
const duplicateEncode = word => {
  word = word.toLowerCase();
  return word.replace(/./g, w => word.indexOf(w) == word.lastIndexOf(w)? '(': ')');
}
```

## 思路解析

总体思路差不多，特殊点在于：

- map的第三个参数是遍历的数组
- indexOf和lastIndexOf直接就能判断出是否出现多次
- 第二种方法利用repalce直接替换，这样省去了split和join感觉很高大上。

## 知识点

- toLowerCase
- split
- map(item, index, arr)
- indexOf
- lastIndexOf
- join
- reg



