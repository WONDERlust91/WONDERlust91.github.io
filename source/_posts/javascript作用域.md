---
title: javascript作用域
date: 2018-03-20 13:50:30
tags:
  - 作用域
  - let
  - const
  - var
categories: javascript
---

在一些语言中，声明和定义存在明确的区分。声明一个变量仅指给它指定一个标志符来宣称它的存在。而定义则代表声明后还要进行赋值。
在 javascript 中，声明和定义是可以互换的概念。因为所有变量都会在声明之后给定一个值。如果你没有显式赋值，会自动给定一个隐含值 undefined。

## 静态作用域

javascript 使用静态作用域。静态作用域又称词法作用域，静态作用域下函数作用域在定义（声明）时已经确定，如未找到自身作用域内的变量，则沿作用域链向上，在上级作用域中查找，直到全局。

<!-- more -->

```javascript
function foo() {
  console.log(a);
}
function bar() {
  let a = 3;
  foo();
}
let a = 2;
bar(); // 输出2 详见下面的说明
```

静态作用域：
foo 的作用域在声明时就已经确定，当 foo 被调用时，在 foo 自身的作用域中找不到变量 a，此时以定义 foo 时的作用域链为准，向作用域链上级查找。foo 定义时的作用域链为：foo 作用域——全局作用域。而在全局作用域中存在 a = 2，故输出结果为 2。

动态作用域：
如果是动态作用域，foo 的作用域在 foo 被调用时确定，作用域链向上是 foo 被调用时的环境。在 foo 的作用域中找不到 a，则沿作用域链向上，在 foo 被调用的环境即 bar 的作用域中查找，bar 中 a = 3，故在动态作用域规则下应输出 3，而 js 使用静态作用域，只能输出 2。

总结：在函数中遇到既不是形参也不是函数内部定义的局部变量的变量时，静态作用域下是去函数定义时的环境中查询，动态作用域下是去函数调用时的环境中查询。

在函数中定义的参数和声明的局部变量，其作用域范围仅在函数内部，当函数调用完毕，参数与局部变量都会被销毁。

## 作用域、上下文、执行环境

**作用域（Scope）**：编译过程中，负责收集并维护所有声明的标志符，确定当前执行代码对这些标志符的访问权限的一套规则。简而言之作用域就是变量的可见性。

**上下文（Context）**：函数调用时的 this 值。this 来自执行环境对象。

P.S. new 关键字调用函数时，上下文 this 被设置为被调用的函数实例，即新构造的对象。

严格模式调用函数，上下文 this 为 undefined。（详见{% post_link javascript关键字this js关键字this %}）

**执行环境（Execution Context）**：执行环境又称执行上下文，当代码流转到可执行代码时，会进入一个执行环境，执行环境有创建和执行两个阶段。创建时会创建一个执行环境对象，其中包含作用域链，变量对象与上下文。
当前所有执行环境构成一个栈，栈底部始终是全局环境，顶部是当前活动的执行环境，随执行流的变化，执行环境入栈或出栈。全局环境的执行环境对象就是 Window 对象。

## 块作用域

ES6 前只有函数作用域和全局作用域，ES6 中添加了块作用域。块是由一对花括号括起来的一系列语句。块作用域指的就是那些仅仅在代码块内有效的变量。块通常是控制流语句的一部分，如 if 或 for，定义独立的块也是合法的。ES6 中 let 与 const 关键字声明的变量处在块作用域中，var 声明的变量在代码块外也可以访问到。

```javascript
const bar = 1;
console.log(foo); // undefined 块内变量提升，相当于只声明，未赋值。
console.log(bar); // 1
{
  var foo = 2; // 相当于已经声明只进行赋值
  const bar = 3; // 块内声明只在块内生效，这里块内声明的变量屏蔽了块外变量，块内无法访问块外变量
  console.log(foo); // 2 undefined 赋值为2
  console.log(bar); // 3
}
console.log(foo); // 2 块外也能访问var声明的变量
console.log(bar); // 1 块外访问不到块内变量bar，只能访问到块外的变量bar
```

## 变量屏蔽

使用 let 或 const 在块作用域外与块作用域内声明相同名字的变量，块内声明的变量会屏蔽块外的，使块外的变量不可访问。这个特性可以防止变量污染。函数作用域同理。

```javascript
const foo = {
  objName: "foo",
};
const bar = foo;
const x = 1;
{
  let foo = 5;
  console.log(foo); // 5 外部foo变量屏蔽，访问不到
  console.log(bar.objName); // foo
  bar.objName = "bar"; // 对象为引用型值，本身并不构成作用域，虽然在块中修改，但是在全局都生效
  let x = 10;
  console.log(x); // 10 外部x变量屏蔽，访问不到
}
console.log(foo); // bar 对象是引用型，变量bar与变量foo引用自相同对象，在bar中更改对象后，foo引用到的也是被修改后的对象
console.log(bar); // bar
console.log(x); // 1 块中变量屏蔽，块外变量不受影响
```

对象是引用型，更底层的解释是对象存储在一个内存位置中，将对象通过变量赋值给其它变量，这些变量都引用自相同内存位置的同一对象，因此在一个变量上操作改变对象，内存中的对象被更改，其他变量引用的也是改变后的对象。
如果将对象直接赋值给不同变量，则每个变量中的对象看起来是相同的，但其实是存储在不同内存位置的不同对象。
const 声明的基本类型值无法被更改，但 const 声明的数组和对象可以被更改，因为对象（数组）是引用型，const 固定了指向对象（数组）内存位置的引用，但对象（数组）在内存中的内容没有固定。

```javascript
const foo = { num: 1 };
const foo1 = foo;
const bar = { num: 1 };
console.log(foo === foo1); // true 通过变量赋值，foo与foo1实际引用自相同对象
console.log(foo === bar); // false 直接赋值，foo与bar是存储在不同内存位置的不同对象，但对象内容相同
foo.num = 2;
console.log(foo === foo1); // true foo修改了内存中的对象，foo1引用自那个对象，所以foo1也被自动更改了
console.log(foo, foo1, bar); // {num: 2}, {num: 2}, {num: 1} foo与foo1引用自相同对象，bar和前两者引用自不同对象
```

## 变量提升

在 ES6 引入 let 和 const 之前，变量都是用 var 来声明的。使用 var 声明的变量在当前作用域中任何地方都可以使用，甚至可以在声明前使用。这是因为使用 var 声明的变量采用了提升机制，javascript 会扫描整个作用域（函数或全局作用域），任何使用 var 声明的变量都会被提升至当前作用域的顶部，被提升的只是对变量的声明，而不是赋值。

```javascript
console.log(foo); // undefined 变量提升，只提升声明，默认赋值为undefined
var foo = 1; // 赋值为1
console.log(foo); // 1
// 等价于
var foo;
console.log(foo);
foo = 1;
console.log(foo);
```

let 与 const 没有变量提升机制，在声明前无法使用

```javascript
console.log(bar); // referenceError 直接报错，无法运行
let bar = 0;
console.log(bar);
```

由于 var 没有块作用域，且 var 有变量提升，故在当前作用域中，尽管可以在块内再次使用 var，但并不会发生变量屏蔽，不能创建同名的新变量。

```javascript
var foo = 0;
if (foo === 0) {
  var foo = 1;
  console.log(foo); // 1
}
console.log(foo); // 1
// 等价于
var foo = 0;
if (foo === 0) {
  foo = 1;
  console.log(foo); // 1
}
console.log(foo); // 1
```

## 临时死区 TDZ

由于 ES6 中 let、const 有块作用域，且 let、const 没有变量提升机制，故在块中再次声明同名变量会不小心产生临时死区。要防止临时死区应注意将所有声明都放在当前作用域顶部。

```javascript
let foo = 0;
if (foo === 0) {
  console.log(foo); // referenceError 这里就是临时死区
  let foo = 1; // 因为块中用了let声明，块内的foo屏蔽了块外的foo，而上一句却在块中未声明foo时就使用foo
  console.log(foo);
}
console.log(foo);
```

ES5 中常用的类型检测方法，在 ES6 中就会因为临时死区而报错

```javascript
if (typeof foo === "undefined") {
  // referenceError
  console.log("foo is safe to use");
}
let foo = 1;
```

## 总结 var、let、const

### var：

1. 有变量提升机制。
2. 没有块作用域，只有函数作用域和全局作用域，块中不会产生变量屏蔽，在函数作用域中才有变量屏蔽。
3. 虽然在同一作用域同一变量名可以多重声明，但不创建新变量，仅相当于赋值操作。
4. 在全局环境声明的变量会自动挂载为 window 的属性，不仅可以通过变量名访问，还可以通过 Window 属性访问，Window.变量名。

### let、const 相同点：

1. 没有变量提升机制。
2. 有块作用域，块内会产生变量屏蔽，警惕因变量屏蔽、无提升机制造成的临时死区。
3. 同一作用域同一变量名只能声明一次。
4. 在全局环境声明的变量不会挂载为 Window 的属性，只能通过变量名访问。

### let、const 区别：

1. let 用于声明变量、const 用于声明常量。
2. const 声明与赋值必须同时进行，基本型值定义后无法更改，如字符串、数字等。
3. 数组、对象为引用型值，const 声明后固定了引用的内存位置，但未固定内容，故数组与对象可更改。

## 函数提升

函数的声明也会被提升至当前作用域的顶部，允许函数在声明前使用。赋值给变量的函数表达式不会提升，作用域规则与变量相同。函数提升更高于变量提升，当函数声明与变量声明同名时，函数声明在前，变量声明在后。（尽量避免函数与变量同名）

```javascript
f(); // f 函数提升，可以提前调用
g(); // referenceError：g未定义  函数未提升，变量未提升，无法调用，直接停止报错
h(); // typeError：h不是函数  函数未提升，变量提升只是声明提升，并未赋值，h为undefined
function f() {
  console.log("f");
}
let g = () => console.log("g");
var h = () => console.log("h");
```

## 严格模式

ES5 允许隐式全局变量，如果忘记使用 var 声明变量，Javascript 会认为你在引用全局变量，如果全局变量不存在，会自动创建一个。

```javascript
function f() {
  a = 1;
  console.log(a); // 1
}
f();
console.log(a); // 1
```

为了防止这种情况以及 this 默认绑定到全局等情况，引入了严格模式。在全局或函数的开头写 "use strict"，单双引号都可以，就开启了严格模式。

由于在全局中开启严格模式会应用到所有脚本代码中，故使用时要谨慎。很多网站在部署前会整合脚本代码，若一个脚本代码用了全局严格模式，则所有代码在整合后都会应用严格模式，为了防止这种情况，应该将用了严格模式的脚本代码封装在一个 IIFE（立即执行函数）中，这样就不会干扰整合后的其它脚本。

```javascript
(function () {
  "use strict";
  // 代码区
})();
```
