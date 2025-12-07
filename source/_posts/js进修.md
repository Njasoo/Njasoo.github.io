---
title: js进修
date: 2025-10-30 15:13:54
tags: ["js"]
---
# 数组是引用传递
数组a和数组b，b=a，然后修改b，这同时也会修改a<br>
下面给大家展示一下茴的四种写法
```js
let b = [...a] // 把数组a展开
let b = a.slice()
let b = Array.from(a)
let b = [].concat(a)
```
但是有个问题，就是这些数组元素如果有对象的话，里面的对象还是浅拷贝的，我大概懂上面这些方法的本质了，其实就是开一个新数组，然后挨个push而已，本质上是一样的

如果要实现深拷贝的话，用这两种
```js
let b = structuredClone(a);
let b = JSON.parse(JSON.stringify(a));
```
JSON.stringify可能应用范围还比较广一点吧，在单独push一个对象的时候也可以用

# 数组重新赋值等于开了新数组
```js
arr = [...tmp]
```
要是这么搞，原本的数组就直接没了，这样通过函数传参传进来的数组也改不了，其实从c++的角度来理解就是，传进来的数组是一维指针

# 用typeof判断数组类型是错误的
会返回object，但实际上数组是一种特殊类型的object，要用这两种方法
```js
Array.isArray([1, 2, 3])
[1, 2, 3] instanceof Array
```

# this的指向
箭头函数的指向从定义开始就固定了，它的this是继承最接近它的外部this（定义时而不是调用时的），它没有自己的this，而function的this指向取决当前最接近它的调用者，挺绕的，我大概精简了

# arguments
函数的参数列表，本质上是一个特殊的对象，要用的时候建议是转换成数组先
```js
let args = [...arguments]
```
还有一个问题，就是，和this一样，箭头函数是没有arguments的