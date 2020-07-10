---
title: 简单Call方法重写
comments: true
date: 2020-07-01 18:14:49
tags:
    - Javascript
categories:
    - 前端
---

> 原理其实就是讲方法绑定到目标对象上，然后执行，执行完毕后清除方法

```js
Function.prototype.call = function() {
    let [thisarg, ...args] = [...arguments]
    if (!thisarg) {
        thisarg = window || global
    }
    thisarg.func = this
    let res = thisarg.func(...args)
    delete thisarg.func
    return res
}
```