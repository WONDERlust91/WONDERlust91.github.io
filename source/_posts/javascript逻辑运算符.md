---
title: javascript逻辑运算符
date: 2020-05-03 23:17:04
tags:
  - 逻辑运算符
  - 与、或、非
categories: javascript
---

前端通常使用逻辑运算符作为条件判断语句的条件，只关心返回值是 truthy 还是 falsy。但非常容易忽略逻辑运算符可以被应用于任意类型值，多个任意类型值的逻辑运算返回值到底是什么。

> 下面所有代码示例中的返回值均指运算返回值，而不是 console.log 函数返回值

## ||（或）

**一个||（或）运算链将返回从左向右第一个真值，如果不存在真值，则返回运算链的最后一个值**，例如：

```javascript
console.log(1 || 0); // 返回值为1（第一个真值）
console.log(true || 'no matter what'); // 返回值为true（第一个真值）
console.log(null || 0 || 1); // 返回值为1（第一个真值）
console.log(null || 0 || undefined); // 返回值为undefined（运算链最后一个值）
```

## &&（与）

**一个&&（与）运算链将返回从左向右第一个假值，如果不存在假值，则返回运算链的最后一个值**，例如：

```javascript
console.log(1 && 0); // 返回值为0（第一个假值）
console.log(true && 'no matter what'); // 返回值为'no matter what'（运算链最后一个值）
console.log(1 && 2 && null && 3); // 返回值为null（第一个假值）
console.log(1 && 2 && 3); // 返回值为3（运算链最后一个值）
```

## !（非）

!（非）运算符将操作数转化为布尔类型，并返回相反的值，两个非运算`!!`用来将某个值转化为布尔类型，也可以使用内置的 Boolean 函数来转换为布尔类型。**非运算符在所有逻辑运算符中优先级最高，它总在`&&`和`||`前执行**。

```javascript
console.log(!null); // 返回值为true
console.log(!!null); // 返回值为false
console.log(Boolean(null)); // 返回值为false
```
