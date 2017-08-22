---
title: CodeWars题目练习 - 2 (数组操作)

date: 2017-8-21

tag: [javascript,codewars,练题]
---

## 题目描述

编写一个函数分解数字：

例如999，共3位，分解第一次 [9, 9, 9] 将其累乘，9 \* 9 \* 9 = 729，

再次分解729，[7, 2, 9]，再次累乘，7 \* 2 \* 9 = 126 ， 

1 \* 2 \* 6 = 12;  

1 * 2 = 2;

直到出现个位数，返回分解的次数。

## 最优解答

```javascript
function persistence(num) {
   var times = 0;
   
   num = num.toString();
   
   while (num.length > 1) {
     times++;
     num = num.split('').map(Number).reduce((a, b) => a * b).toString();
   }
   
   return times;
}
```

## 思路解析

- 先将数字以字符串的形式分割
- 然后再用reduce函数进行累乘
- 再通过while语句判断位数。
- 期间以times记录分解次数，用以最终返回。

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

- while

## 附一小题：

求一个数组最小的两个数的和：

```java
function sumTwoSmallestNumbers(numbers){  
  	numbers = numbers.sort(function(a, b){return a - b; });
  	return numbers[0] + numbers[1];
};
```
