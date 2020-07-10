---
title: event对象中target和currentTarget属性的区别
comments: true
date: 2020-06-13 18:09:25
tags:
    - Javascript
categories:
    - 前端
---

target: 返回触发事件的元素
currentTarget: 返回绑定事件的元素

```
<ul id='ul'><li>click</li></ul>

document.getElementById('ul').onclick = function(e) {
  // 点击li  e.target 为li
  // 点击li e.currentTarget 为ul （点击ul中任何地方 都返回 ul)
}
```