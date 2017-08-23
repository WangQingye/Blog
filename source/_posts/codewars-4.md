---
title: CodeWars题目练习 - 4 (数组取最小)

date: 2017-8-23

tag: [javascript,codewars,练题]
---

## 题目描述

删除一个数组中最小的元素（不改变数组顺序）。

## 常规解答

```javascript
function removeSmallest(numbers) {
  if(!numbers)return [];
  var min=Math.min.apply(null,numbers);
  numbers.splice(numbers.indexOf(min),1);
  return numbers;
}
```

- 借用Math的min方法
- spilce掉最小的那个index

## 最优解答

```javascript
 function removeSmallest(numbers) {
  let indexOfMin = numbers.indexOf(Math.min(...numbers));
  return [...numbers.slice(0, indexOfMin), ...numbers.slice(indexOfMin + 1)];
}
```

## 思路解析

- 思路同上，但是好处是用了ES6的新特性扩展运算符。
- 用silce拼接的好处是不会改变numbers。

## 知识点

- apply
- splice
- indexOf
- 扩展运算符...
- slice

## 附一小题：

筛选出一个数组中的数字；

```javascript
function filter_list(l) { 
  return l.filter(function(item){
    return typeof item === 'number'
  })
}
```

