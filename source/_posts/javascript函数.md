---
title: javascript函数
date: 2018-03-14 20:26:47
tags: 函数
categories: javascript
---
### 函数中的参数、非参数、全局、局部变量作用域问题
1. 给函数的参数赋值或在函数中声明与外部相同的变量都不影响外部的变量；
2. 若在函数中，对非声明变量或非参数变量赋值，即对全局变量赋值，会改变外部的全局变量。

即函数的参数与函数中声明的变量都是在函数的内部作用域中，不影响全局作用域。
<!-- more -->
```javascript
let a = 0, b = 1, c = 2;
function foo (b) {
    let a = 3;
    b = 4;
    c = 5;
    console.log(`In foo a = ${a}, b = ${b}, c = ${c}`);
};
console.log(`In global a = ${a}, b = ${b}, c = ${c}`);
// In foo a = 3, b = 4, c = 5
// In global a = 0, b = 1, c = 5
```
#### javascript中基本类型是对原始值的复制，对象是引用型，被赋值的变量和原变量都是对同一对象的引用。复制型会占用更多的内存，不同变量虽然是相同的值，但使用了不同的内存地址。引用型，不同的变量引用相同内存地址的相同对象。
#### 可以使用任意多个参数去调用任何一个函数，如果调用时没有传入参数，函数就会接收到一个undefined参数。
#### ES6：在函数定义中使用展开操作符“...”，展开操作符之后必需为最后一个参数。展开操作符会收集多余的参数，并将多余参数放入以展开操作符后的参数命名的数组中。
```javascript
function addPrefix(prefix, ...words) {
    const prefixedWords = [];
    for (let i = 0; i < words.length; i++) {
        prefixedWords.push(prefix + words[i])
    }
    return prefixedWords;
}
addPrefix("con", "verse", "vex")
// prefixedWords = ["converse", "convex"]
```
#### ES6：函数作为对象属性的快捷语法
```javascript
const o = {
    name: "Wallace",
    bark: function () { return "Woof!"; },
}
// 上述对象写法等效于
const o = {
    name: "Wallace",
    bark() { return "Woof!"; },
}
```
### 具名立即调用函数表达式（具名IIFE）对自身再赋值
```javascript
function a(){ // a是函数
    a = 1; // a被赋值为1
    console.log(a); // 1
};
a();
(function a(){ // a是函数表达式
    a = 1; // 这句不执行，无效
    console.log(a); // function a(){...}
})();
```
1. IIFE是函数表达式，不是函数
2. 函数表达式与函数不同，函数表达式的函数名只在函数内部生效，且绑定为一个常数
3. 对一个常数再赋值，在严格模式下报错，在非严格模式下赋值语句无效