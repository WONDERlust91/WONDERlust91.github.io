---
title: javascript日期类型转换
date: 2019-02-11 15:45:10
tags:
  - javascript
  - 日期
  - 类型转换
categories: javascript
---

# + newDate() 是什么意思？

我们都知道 javascript 是一种弱类型的语言，类型是可以被转换的。在实际应用中我们常常会看到这样一种获取毫秒数的方法：+ new Date()。这是什么原理呢？

为什么+ new Date() = new Date().getTime()。原理是 js 类型转换，+ new Date()相当于对new Date()进行类型转换，将 Date 类型转换为 Number 类型，若转换失败会得到 NaN。+ new Date()调用了 Date.prototype 上的 valueOf 方法，该方法等同于 Date.prototype.getTime()。
