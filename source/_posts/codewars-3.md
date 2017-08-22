---
title: CodeWars题目练习 - 3 (数组操作)

date: 2017-8-22

tag: [javascript,codewars,练题]
---

## 题目描述

假设有一个只包含数字的数组：例如[1, 2, 3, 4, 3, 2, 1]

找到一个index，他两边的所有数字和值相等。

如上面的数组，当 index = 3 时， 1 + 2 + 3 = 6， 3 + 2 + 1 = 6;

再入[3, -1, 1]，这个时候应该返回 0 ， 因为0的左边没有数字了，所以和为0，而右边1 + -1 = 0。

如果没有这个index，返回-1

## 常规解答

```javascript
function findEvenIndex(arr)
{
  for(var i=1; i<arr.length-1; i++) {
    if(arr.slice(0, i).reduce((a, b) =>  a+b) === arr.slice(i+1).reduce((a, b) =>  a+b)) {
      return i;
    }
  }
  return -1;
}
```

利用reduce对两边的数组求和，这是最常规的解答，但是代价是消耗大，因为每一次reduce都会对数组进行遍历。

## 最优解答

```javascript
function findEvenIndex(arr) {
    var i,
        left,
        right = 0,
        len = arr.length;

    if (len === 0) {
        return -1;
    }

    left = arr[0];

    for (i = 1; i < len; i++) {
        right += arr[i]; // 一开始右边的和值是这个数组的和值
    }

    for (i = 1; i < len; i++) {
        right -= arr[i]; // 没偏移一个，将最左边的数字减去
        if (left === right) {
            return i;
        }
        left += arr[i]; // 没进行完一次，将这个数字加给左边
    }

    return -1;
}
```

## 思路解析

- 因为数组是固定不变的，所以index的每一次偏移，对于左右的和值变化量来说，其实就是对应index的值。

## 知识点

- reduce

  **reduce()** 方法对累加器和数组中的每个元素 (从左到右)应用一个函数，将其减少为单个值。

  ```javascript
  var total = [0, 1, 2, 3].reduce(function(sum, value) {
    return sum + value;
  }, 0);
  // total is 6

  var flattened = [[0, 1], [2, 3], [4, 5]].reduce(function(a, b) {
    return a.concat(b);
  }, []);
  // flattened is [0, 1, 2, 3, 4, 5]
  ```

  function的第一个参数为执行到现在的累值，后一个为当前要操作的值。

- slice(0,1) 包括0，不包括1;

## 附一小题：

求小于一个num的所有能除尽3和5的数的和。（同时除尽只加一次）

```javascript
function solution(number){
  var sum = 0;
  
  for(var i = 3;i< number; i++){
    if(i % 3 == 0 || i % 5 == 0){
      sum += i
    }
  }
  return sum;
}
```

