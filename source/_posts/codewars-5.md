---
title: CodeWars题目练习 - 5 (数组递归求和)

date: 2017-8-23

tag: [javascript,codewars,练题]
---

## 题目描述

例：输入一个正整数，823，

8 + 2 + 3 = 13

1 + 3 = 4

返回4

## 常规解答

```javascript
function digital_root(n) {
  // ...
  var sum = n;
  while(parseInt(sum) > 10)
  {
    sum = sum.toString().split('').reduce(function(a, b){
          return a += parseInt(b);
        }, 0)
  }
  return sum;
}

```

- 拆分成单个数字然后求和，直到返回个位数

## 最优解答

```javascript
function digital_root(n) {
  return (n - 1) % 9 + 1;
}
```

## 思路解析

- 这已经是数学的范畴。。。。。

## 知识点

- parseInt
- toString
- split
- reduce
- while

## 附一小题：

筛选出一个数组中的数字；

```javascript
function filter_list(l) { 
  return l.filter(function(item){
    return typeof item === 'number'
  })
}
```

