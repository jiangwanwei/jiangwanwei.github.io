---
title: 获取手机号位置api
comments: true
date: 2017-02-02 22:22:10
tags: [API]
categories: [前端]
---

## 淘宝网

API地址： http://tcc.taobao.com/cc/json/mobile_tel_segment.htm?tel=15850781443

参数：
tel：手机号码

返回：JSON


## 拍拍

API地址： http://virtual.paipai.com/extinfo/GetMobileProductInfo?mobile=15850781443&amount=10000&callname=getPhoneNumInfoExtCallback

参数：

mobile：手机号码

callname：回调函数

amount：未知（必须）

返回：JSON

## 财付通

API地址： http://life.tenpay.com/cgi-bin/mobile/MobileQueryAttribution.cgi?chgmobile=15850781443

参数：

chgmobile：手机号码

返回：xml

## 百付宝

API地址： https://www.baifubao.com/callback?cmd=1059&callback=phone&phone=15850781443

参数：
phone：手机号码

callback：回调函数

cmd：未知（必须）

返回：JSON

## 115

API地址： http://cz.115.com/?ct=index&ac=get_mobile_local&callback=jsonp1333962541001&mobile=15850781443

参数：

mobile：手机号码

callback：回调函数

返回：JSON

## 有道api接口

接口地址：http://www.youdao.com/smartresult-xml/search.s?type=mobile&q=13892101112

参数说明：

type ： 参数手机归属地固定为mobile

q ： 手机号码

返回XML格式：
`<?xml version="1.0" encoding="gbk"?>
    <smartresult> 
        <product type="mobile"> 
        <phonenum>13892101112</phonenum> 
        <location>陕西 延安</location> 
        </product> 
    </smartresult>
`

或者

http://www.youdao.com/smartresult-xml/search.s?jsFlag=true&type=mobile&q=手机号码

返回JSON格式：

```
fYodaoCallBack(1, {
    ‘product’:'mobile’,
    'phonenum’:’13892101112′,
    ’location’:'陕西 延安’
    } , 
);
```


## 096.me api接口

查询手机号码归属地：
http://www.096.me/api.php?phone=手机号&mode={txt,xml}

举例：http://www.096.me/api.php?phone=13892101111&mode=txt

返回：

13892101111||陕西延安移动全球通卡 ||吉凶参半，惟赖勇气，贯彻力行，始可成功 吉带凶||

举例：http://www.096.me/api.php?phone=13892101111&mode=xml

返回：

13892101111 陕西延安移动全球通卡 吉凶参半，惟赖勇气，贯彻力行，始可成功 吉带凶