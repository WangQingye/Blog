---
title: CodeWars题目练习 - 1 (转换字符串)

date: 2017-8-21

tag: [javascript,codewars,练题]
---

## 题目描述

为字符串String原型添加一个方法toJadenCase()。

作用如下：

​	输入字符串 'Today is a good day'  输出==> 'Today Is A Good Day' (将每个单词都转换为首字母大写)

## 最优解答

```javascript
String.prototype.toJadenCase = function () { 
  return this.split(" ").map(function(word){
    return word.charAt(0).toUpperCase() + word.slice(1);
  }).join(" ");
}
```

## 思路解析

- 通过split方法以空格为标志将字符串切分为字符串片段的数组，例 ['Today', 'is', 'a', 'good', 'day']
- 通过map方法遍历数组，将数组中的字符串的首字母大写。（map方法会返回执行后的数组）。
- 最后通过join方法将数组拼接会字符串。

## 知识点

- prototype
- split
- map
- charAt
- toUpperCase
- slice
- join
