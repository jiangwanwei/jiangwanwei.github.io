---
title: Js的New发生了什么
comments: true
date: 2020-07-03 18:18:03
tags:
    - Javascript
categories:
    - 前端
---

*js new 做了什么？*

1、创建一个空对象

2、空对象原型指向目标方法的原型

3、执行方法目标方法

4、返回新对象

```javascript

// 方式1
function newMethod(Target, ...rest) {
  // 1
  let o = {}
  // 2
  o.__proto__ = Target.prototype
  // 3
  Target.apply(o, rest)
  // 4
  return o
}

// 方式2
function newMethod(Target, ...rest) {
    // 1.以构造器的prototype属性为原型，创建新对象；
    let o = Object.create(Target.prototype)
    // 2.将this和调用参数传给构造器执行
    Target.apply(o, rest)
    // 3.返回第一步的对象
    return o
}

```