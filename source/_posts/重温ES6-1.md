---
title: 重温ES6 - 1
date: 2017-9-27
tag: [javascript]
---

公司中老项目是运用ES5写成了，现在全面升级ES6，固将以前学习过ES6的知识在重温一遍。

## let和const

```javascript
// es6
for (let i = 1; i < 3; i++){
  console.log(i); // 1 2
}
console.log(i) // 找不到i
// 因为ES6默认开启了严格模式 let生效的地方就是块作用域。
// 最简单的判断块作用域的方式就是：一个大括号里面的就是一个块作用域。

// es5
for (var i = 1; i < 3; i++){
  console.log(i); // 1 2
}
console.log(i) // 3
==================================================
  
let a = 1;
let a = 2; // 编译报错，不能重复定义变量

==================================================
const PI = 3.14;
PI = 3 // 报错，const是常量，不能修改。

const k = { a:1 };
const k.b = 3; //不会报错，对象在内存中的指针未变。

const PI; // 报错，常量声明时必须赋值
```

## 解构赋值

数组解构赋值、对象解构赋值、字符串解构赋值、布尔值解构赋值、函数参数解构赋值、数值解构赋值等。

```javascript
// 数组解构赋值
{
  let a,b,rest;
  [a,b] = [1,2];
  [a,b,...rest] = [1,2,3,4,5]
  console.log(a, b, rest) // 1 2 [3, 4, 5]
  
  [a, b, c=3] = [1,2];
  console.log(a,b,c) // 1 2 3
  [a, b, c] = [1,2];
  console.log(a,b,c) // 1 2 undefined
}

//对象的解构赋值
{
  let a,b;
  ({a, b} = {a:1, b:2})
  console.log(a, b);
}
```

几种常用场景：

```javascript
// 变量换值
{  
  let a = 1;
  let b = 2;
  [a,b] = [b,a] 
}
// 函数返回多个变量
{
  function f(){
    return [1,2]
  }
  let a,b;
  [a, b] = f();
  
  // 跳跃赋值
  function f(){
    return [1,2,3]
  }
  let a,b;
  [a,,b] = f(); // a = 1 , b = 3
}

// JSON
{
  let metaData = {
    title: 'abc',
    test: [{
      title: 'test',
      desc: 'desc'
    }]
  }
  let {title:esTitle, test:[{title:cnTitle}]} = metaData;
  console.log(esTitle, cnTitle) // abc test
}
```

## 正则扩展

```javascript
{
  // es5 中两种正则的构造方式
  let regex = new RegExp('xyz', 'i');
  let regex1 = new RegExp(/xyz/i);  
  console.log(regex.test('xyz123'), regex1.test('xyz123')); // true true
  
  // es6中新增的一种
  let regex2 = new RegExp(/xyz/ig, 'i') // 后面的i修饰符可以覆盖前面的ig
  console.log(regex2.flags) // i  
}

{
  let s = bbb_bb_b;
  let a1 = /b+/g;
  let a2 = /b+/y;
  
  console.log('one', a1.exec(s), a2.exec(s)); // bbb  bbb ["bbb", index: 0, input: "bbb_bb_b"]
  console.log('two', a2.exec(s), a2.exec(s)); // bb  null
  a1.sticky // 是否开启了y修饰符
}

{
  let s = `\u{20BB7}`; // 一个大于两个字节的Unicode字符
  console.log('u', /^.$/.test(s));   // false
  console.log('u', /^.$/u.test(s));  // true
  // u修饰符，用于判断大于两个字节的Unicode字符
}
```

## 字符串扩展

```javascript
{
  console.log('a', `\u0061`); // a a
  console.log('s', `\u20BB7`); // s □7 处理错误
  console.log('s', `\u{20BB7}`) // s 𠮷 大括号包起后可以处理大于两位的Unicode
  let s = 𠮷;
  s.length // 2 
  
  // 一些新方法
  .codePointAt => charAt/charCodeAt
  .fromCodePoint => codePointAt
  
  let str = 'string';
  str.includes('s') // true;
  str.startWith('s') // true;
  str.endWith('ng') // true
  str.repeat(2) // 'stringstring'
  
  // 模板字符串
  let name = 'list';
  let info = 'hello world';
  let m = `i am ${name}, ${info}` // i am list, hello world
  
  // 补白
  1.padStart(2, '0') // 01 比如补日期
  1.padEnd(2, '0') // 10
  
  // 标签模板
  let user = {
    name: 'list',
    info: 'hello world'
  };
  abc`i am ${user.name}, ${user.info}`
  function abc(s, v1, v2){
    console.log(s, v1, v2) ["i am", ",", "", raw:Array[3]] "list" "hello world"
  }
  
  //raw （在所有斜杠前再加一个斜杠）
  String.raw`Hi\n${1+2}` Hi\n3   // 注意没有括号
  `Hi\n${1+2}` Hi
  			   3
}
```

## 数值扩展

```javascript
console.log(0B111110111) // 503 二进制0B
console.log(0O767) // 503 八进制0O

{
  Number.isFinite(15) // true
  Number.isFinite(1/0) // false
  Number.isFinite(NaN) // false
  
  Number.isInteger(25) // true  
  Number.isInteger(25.0) // true
  Number.isInteger('25') // false
  MAX_SAFE_INTEGER // JS中最大的整数 MIN 最小 Number.isSafeInteger用于判断是否在这个区间
  
  // 取整
  Math.trunc(4.1) // 4  
  Math.trunc(4.9) // 4
  
  // 判断正负
  Math.sign(-5) // -1;
  Math.sign(0) // 0;
  Math.sign(5) // 1
}
```