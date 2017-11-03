---
title: JS判断一个点是否在多边形内
comments: true
date: 2017-02-02 20:55:10
tags:
    - JavaScript
categories:
    - 前端
---
```
//定义点的结构体
function point(){
    this.x=0;
    this.y=0;
}
//计算一个点是否在多边形里,参数:点,多边形数组
function PointInPoly(pt, poly) { 
    for (var c = false, i = -1, l = poly.length, j = l - 1; ++i < l; j = i) 
        ((poly[i].y <= pt.y && pt.y < poly[j].y) || (poly[j].y <= pt.y && pt.y < poly[i].y)) 
        && (pt.x < (poly[j].x - poly[i].x) * (pt.y - poly[i].y) / (poly[j].y - poly[i].y) + poly[i].x) 
        && (c = !c); 
    return c; 
}
 
//调用
var pt=new point();
pt.x=34.32812720562833;
pt.y=108.93115997314453;
var _poly=[{x:1.1,y:1.1},{x:3,y:1},{x:6,y:4},{x:2,y:10},{x:1.1,y:1.1}];

var xx=PointInPoly(pt,_poly);
alert(xx);
```