---
title: 一个一键压缩图片的小脚本

date: 2017-8-18

tag: [脚本,node.js]
---

新公司的项目中，因为单个项目都比较小，所以并没有引入打包工具(gulp或者webpack等)，大家普遍在自己去tinypng网站自己压缩图片。面对这个问题，就自己写了一个小脚本，通过调用tinypng的API实现一键压缩。

### 目的：

压缩指定文件夹下的png和jpg图片。

### 原理：

采用tinypng的api，优点是压缩度大，质量好，缺点是每月限额500张。

### 开发过程：

1. 引入了一个叫做tinify的npm包：

   ```javascript
   const tinify = require("tinify");

   /* 这个api是在 https://tinypng.com/developers 申请的，每个邮箱每个月只能压500张 */

   tinify.key = "lgwr26XdUj-ExIlL8O_mPwb1-Uadp5fL";

   tinify.fromFile(filePath).toFile(filePath);
   ```

2. 扫描一个指定文件夹里所有png和jpg图片：

   ```javascript
   function readFile(path) {
       var files = fs.readdirSync(path);
       files.forEach(ergodic);
       function ergodic(file) {
           var filePath = path + '/' + file;
           var states = fs.statSync(filePath);
           if (states.isDirectory())
           {
               // 如果是一个文件夹，就再次读取里面
               readFile(filePath);
           } else
           {
               // 看一下是不是图片， 是的话就开始压缩
               if (filePath.substr(-3, 3) === 'png' || filePath.substr(-3, 3) === 'jpg')
               {
                   tinify.fromFile(filePath).toFile(filePath);
               }
           }
       }
   }
   ```

### 用法：

在当前文件夹app\dev-static\tester\tinyimg下打开命令行

输入"node tinyimg {你要压缩的文件夹}"

注：文件夹路径是以app/v2为起点

### 示例：

```javascript
node tinyimg zt/2017/whisper
node tinyimg views/musician
```

### 注意事项：

- 暂时只支持 zt和views两个文件夹。