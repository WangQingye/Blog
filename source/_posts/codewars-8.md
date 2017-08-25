---
title: CodeWars题目练习 - 7 (数组筛选)

date: 2017-8-25

tag: [javascript,codewars,练题]
---

## 题目描述

将数组中的0都移到最后去，不改变其他元素的顺序。

moveZeros([false,1,0,1,2,0,1,3,"a"]) // returns[false,1,1,2,1,3,"a",0,0]

## 常规解答

```javascript
var moveZeros = function (arr) {
  // TODO: Program me
  var times = 0;
  arr = arr.filter(function(item){
    if (item === 0){
      times++;
      return false;
    }
    return true;
  })
  for(let i = 0; i < times; i++)
  {
    arr.push(0)
  }
  return arr
}
```

- 计算数组中有多少0，并将不是0的筛选出来
- 注意===，因为false==0

## 最优解答

```javascript
#1
var moveZeros = function (arr) {
  return arr.filter(function(x) {return x !== 0}).concat(arr.filter(function(x) {return x === 0;}));
}
```

## 思路解析

- 筛选出不是0的，再筛选出0，再合并。

## 知识点

- filter，筛选出符合条件的元素（根据结果是true还是false）
- concat，返回两个数组合并后的数组。


## 附一小题

将一个字符串或数组中重复的元素去除 

例子：

‘aaabbcc’ => 'abc' 

 [1,1,1,2,2,3,4] => [1,2,3,4]

```javascript
var uniqueInOrder = function (iterable)
{
  return [].filter.call(iterable, (function (a, i) { return iterable[i - 1] !== a }));
}
```

