---
title: Promise.all 方法的实现

date: 2019-6-12

tag: [javascript]

---

##### 功能

Promise.all(iterable) 返回一个新的Promise实例。此实例在`iterable`参数内所有的promise都`fullfilled`或者参数种不包含`promise`时，状态变成`fullfilled`；如果参数中promise有一个失败`rejected`，此实例回调失败，失败原因是第一个失败promise的返回结果。

##### 例子

```javascript
let p = Promise.all([p1, p2, p3])
```

p 的状态由p1,p2,p3决定：

1. 只有p1,p2,p3的状态都变成了fullfilled, p 的状态才会变成fullfilled, 此时p1,p2,p3的返回值组成一个数组，传递给p的回调函数。
2. 只要其中有一个的状态变成了rejected，p的状态就会变成rejected，此时第一个被rejected的实例的返回值就会传入p的回调函数。

##### 实现：

```javascript
Promise.all = function (promises) {
    let results = new Array(promises.length);
    let index = 0;
    return new Promise((resolve, reject) => {
        if (promises.length == 0) {
            resolve([]);
        } else {
            promises.forEach((p,i) => {
                //通过Promise.resolve方法得到每个promise实例的结果
                Promise.resolve(p).then(d => {
                    results[i] = d;
                    if (++index === promises.length) {
                        resolve(results);
                    }
                }, err => {
                    reject(err);
                })
            });
        }
    })
}
```

