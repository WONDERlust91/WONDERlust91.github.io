---
title: javascript判断类型
date: 2018-11-18 20:14:52
tags:
- javascript
- 类型
categories: javascript
---
# javascript判断类型

## typeof运算符

```javascript
typeof "a"; // "string"
typeof a; // "undefined" 未声明变量
typeof 1; // "number"
typeof function () {}; // "function"
typeof (() => {}) // "function"
typeof (function() {})(); // "undefined"
typeof (() => {})(); // "undefined"
typeof undefined; // "undefined"
typeof null; // "object"
typeof []; // "object"
typeof {}; // "object"
```

通过上面的例子可以看出typeof运算符可以判断出除了null、array外的基本类型，IIFE则以return的值为准，无return语句就是默认return了undefined

## instanceof运算符

instanceof运算符用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置，这样就可以区分Array与Object

```javascript
const arr = [];
arr instanceof Array; // true
```

## constructor属性

还有一种方法就是利用对象的constructor属性，也可以区分Array和Object

```javascript
const arr = [];
arr.constructor === Array; // true
```

## ES6中新增了Array.isArray()方法来判断是否为数组

```javascript
const arr = [];
Array.isArray(arr); // true
```