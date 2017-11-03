---
title: JS长数字格式化
comments: true
date: 2017-01-22 12:13:21
tags: [JavaScript]
categories: [前端]
---

```
/* 数字格式化 num:目标数字 precision:小数点后位数(默认所有) separator:分隔符(默认',') */
function formatNumber(num, precision, separator){
    var parts;
    // 判断是否为数字
    if (!isNaN(parseFloat(num)) && isFinite(num)) {
        // 把类似 .5, 5. 之类的数据转化成0.5, 5, 为数据精度处理做准, 至于为什么// 不在判断中直接写 
        if (!isNaN(num = parseFloat(num)) && isFinite(num))
            // 是因为parseFloat有一个奇怪的精度问题, 比如 parseFloat(12312312.1234567119)
            // 的值变成了 12312312.123456713
        num = Number(num);
        // 处理小数点位数
        num = (typeof precision !== 'undefined' ? num.toFixed(precision) : num).toString();
        // 分离数字的小数部分和整数部分
        parts = num.split('.');
        // 整数部分加[separator]分隔, 借用一个著名的正则表达式
        parts[0] = parts[0].toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1' + (separator || ','));

        return parts.join('.');
    }
    return NaN;
}

测试：
formatNumber(10000)
"10,000"
formatNumber(10000, 2)
"10,000.00"
formatNumber(10000.123456, 2)
"10,000.12"
formatNumber(10000.123456, 2, ' ')
"10 000.12"

正则表达式方法：result = "1234567890".replace(/\d+?(?=(?:\d{3})+$)/img, "$& ");
```