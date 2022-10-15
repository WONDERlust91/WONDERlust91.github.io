---
title: javascript错误处理机制
date: 2021-06-04 13:55:10
tags:
  - Error
  - 错误处理
  - javascript
categories: javascript
---

## Error 对象

JavaScript 解析或运行时，一旦发生错误，引擎就会抛出一个错误对象。JavaScript 原生提供 Error 构造函数，所有抛出的错误都是这个构造函数的实例。

### 属性

ECMA-262 规定了 Error 对象包括两个属性：message 和 name。message 保存错误的信息，而 name 保存错误类型。浏览器厂商还对 error 对象属性做了扩展，添加了其他相关信息。其中实现最广泛的是 stack 属性，表示错误的栈信息。

可以使用 Error 构造函数来创建错误对象，如果指定 message 参数，则该 Error 对象将把它用作 message 属性；若不指定，它将使用空字符串作为 message 属性。

当不使用 new 操作符，直接使用 Error 构造函数像一个普通函数一样调用时，它的行为与使用 new 操作符时一致。

### 方法

Error 对象还有一个 toString 方法，该方法覆盖了对象原型上的 toString 方法，返回代表对象类型与信息的字符串。

<!-- more -->

## 错误类型

### 原生错误类型

ECMA-262 共定义了 8 种错误类型（InternalError 非标准错误类型，不推荐使用），其中包含最基础的错误类型 Error。

- Error 基础错误类型，其他错误类型继承自该类型，Error 类型错误很少见，这个类型主要供开发人员抛出自定义类型错误
- EvalError 表示 eval()函数中发生的错误，ES5 后已不再使用 eval 函数，故该错误类型也不再出现，只是为了向前兼容仍然保留
- RangeError 表示当数值变量或参数超出其有效范围时发生的错误
- ReferenceError 表示引用了无效引用发生的错误
- SyntaxError 表示语法错误
- TypeError 表示当变量或参数不是有效类型时发生的错误
- URIError 表示当 encodeURI()或 decodeURI()传递无效参数时发生的错误
- AggregateError 表示多个错误包裹在一个错误中，例如在 Promise.any()中，当多个错误需要在一个操作中报告
- InternalError (deprecated，非标准) 表示引发 JavaScript 引擎内部错误时发生的错误，例如，无限递归错误

### 自定义错误类型

除原生提供的几种错误类型外，还可以基于基础的错误类型，定义自己的错误对象。

```js
// 声明自定义错误类型
function CustomedError(message) {
  this.message = message || "";
  this.name = "CustomedError";
}
CustomedError.prototype = new Error();
CustomedError.prototype.constructor = CustomedError;
// 使用自定义错误类型
new CustomedError("这是自定义的错误！");
```

思考：这里是用原型来实现的，ES 如何用 class 来实现？

## throw 语句

使用 throw 语句抛出异常，当抛出异常时，throw 后面所跟的表达式表示异常值，异常值可以为任何类型。

```js
throw "Error2"; // generates an exception with a string value
throw 42; // generates an exception with the value 42
throw true; // generates an exception with the value true
throw new Error("Required"); // generates an error object with the message of Required
```

特别要注意的是：**抛出错误的过程是阻塞的，后续的代码将中断不会执行**

## try catch 语句

try catch 语句标记了要尝试的语句块，并定义了有异常抛出时的响应。

```js
try {
  // 要执行的语句
} catch (exception_var) {
  // exception_var 可选参数，throw抛出的异常变量
  // 若try语句块中抛出错误时需要执行的语句
} finally {
  // try语句执行后，无论是否有异常抛出还是捕获，都要执行的逻辑
}
```

try catch 语句块必需包含一个 catch 或 finally 块，或两个都包括。

```js
// 包含一个catch块
try {
} catch {}
// 包含一个finally块
try {
} finally {
}
// 都包含
try {
} catch {
} finally {
}
```

若有错误从 try 块中抛出，程序会立刻跳转至 catch 块，否则 catch 块会被跳过；finally 块总是在 try 块和 catch 块执行完成后执行，无论是否有异常抛出。

你甚至可以嵌套 try catch 语句，若内部的 try 块没有对应的 catch 块，则会使用最近父级的 catch 块。

一个常见的用法是只捕获一小部分预期的错误，然后在其他情况下重新抛出错误：

```js
try {
  // 尝试执行的语句
} catch (e) {
  if (e instanceof RangeError) {
    // 捕获范围类型错误
  } else {
    // 继续抛出其他类型错误
    throw e;
  }
}
```

任何嵌套 try catch 语句中的异常都只会被最近的 catch 块捕获一次，除非这个异常再次被抛出。当然在内部抛出的任何新异常，都会被外部捕获。

需要特别注意的一点是：**如果 finally 块中有返回值，这个值将成为整个 try catch 语句的返回值，无论 try 块和 catch 块中有没有 return 语句。**这也从侧面反映出，不论 try 块与 catch 块中是否有 return 语句，都不会影响 finally 块的执行。

> 参考资料
>
> [javascript 中的错误处理机制-小火柴的蓝色理想](https://www.cnblogs.com/xiaohuochai/p/5677490.html)
>
> [错误处理机制-阮一峰](https://javascript.ruanyifeng.com/grammar/error.html#toc0)
>
> [MDN-Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)
>
> [MDN-throw](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/throw)
>
> [MDN-try...catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)
