---
title: CodeWars题目练习 - 7 (数组遍历)

date: 2017-8-24

tag: [javascript,codewars,练题]
---

## 题目描述

有两个数字数组a，b如b的每个元素都是a中某一个元素的平方，则返回true，否则false

例子 ： 	a = [121, 144, 19, 161, 19, 144, 19, 11]

​		b = [121, 14641, 20736, 361, 25921, 361, 20736, 361]

转化后：

​		a = [121, 144, 19, 161, 19, 144, 19, 11] 
​		b = [11\*11, 121\*121, 144\*144, 19\*19, 161\*161, 19\*19, 144\*144, 19\*19]

如果b中任何一个元素改变成一个其他数字（不符合条件的）都会返回false

## 常规解答

```javascript
function comp(array1, array2){
  if (!array1 || !array2) return false;  
  array1.forEach(function(item){
    var index = array2.indexOf((item * item));
    if (index !== -1)
    {
      array2.splice(index, 1)
    }
  })
  return array2.length == 0;
}
```

- 将a中每个元素平方，去b中寻找
- 如果b中有这个元素，则从b中删除，最后看b中是否还有元素。
- 缺点是会改变b数组的原结构。

## 最优解答

```javascript
#1
function comp(array1, array2) {
  if(array1 == null || array2 == null) return false;
  array1.sort((a, b) => a - b); array2.sort((a, b) => a - b);
  return array1.map(v => v * v).every((v, i) => v == array2[i]);
}

#2
function comp(array1, array2) {
  let arr1 = array1? array1.map(e => e * e).sort((a, b) => a - b): 1;
  let arr2 = array2? array2.sort((a, b) => a - b).join(''): 1;
  return arr1.join('') == arr2;
}
```

## 思路解析

- 两种方式都是排序然后一一去对应
- 第二种方式的优势是直接将数组转换成字符串，少了一次遍历。
- 还值得一提的是少了一个判断null的语句，因为最后return的时候如果是null就不会相等。

## 知识点

- forEach，无返回值，对数组的每个元素进行一次操作。
- sort
- map 返回一个新数组，每个元素为调用func的结果。
- every 返回一个boolean，判断是否每个元素都符合func的条件
- 数组判断相等可以sort后转换成字符串。


## 附一小题

将一个字符串里的所有字母取出，返回其顺序 （a - 1, z - 26）

```javascript
function alphabetPosition(text) {
  return text
    .toUpperCase()
    .match(/[a-z]/gi)
    .map( (c) => c.charCodeAt() - 64)
    .join(' ');
}
```

