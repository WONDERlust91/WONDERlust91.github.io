---
title: javascript闭包
date: 2018-03-23 20:19:11
tags: 闭包
categories: javascript
---
# 什么是闭包？
函数记住（记住就是指不被垃圾回收）并访问所在的词法作用域，叫做闭包现象，函数对作用域的引用就叫闭包。（个人理解：闭包就是函数与其所在的不被垃圾回收掉的词法作用域的引用关系）
# 闭包的三个条件
## 一、可以访问其所在作用域的函数
即可以通过作用域链查找变量的函数
```javascript
const foo = 0;
function bar() {
    console.log(foo);
}
bar(); // 0
```
## 二、有权访问另一个函数作用域中变量的函数
即访问上层函数作用域的内层函数
```javascript
function foo() {
    const a = 0;
    function bar() {
        console.log(a);
    }
    bar();
}
foo(); // 0
```
## 三、在函数声明时的作用域以外的地方被调用的函数
即函数作为返回值或作为参数被传递
```javascript
// 作为返回值
function foo() {
    const a = 0;
    return function () {
        console.log(a);
    };
}
foo()(); // 0
```
```javascript
// 作为参数传递
function foo() {
    const a = 0;
    function bar() {
        console.log(a);
    }
    baz(bar);
}
function baz(fn) {
    fn();
}
foo(); // 0
```
<!-- more -->
## 闭包的作用
闭包通常用来间接访问变量。即访问变量时还要隐藏变量，避免变量被全局变量污染。
## 为什么要满足三个条件
通过闭包的作用，来分析闭包满足三个条件的原因：

在ES6以前，javascript只有全局作用域和函数作用域，并没有块作用域的概念，故想要隐藏变量只能将变量放在局部作用域中，函数作用域就是唯一的局部作用域。函数嵌套中，外层函数提供了局部作用域，将要隐藏的变量放在其中。内层函数由于要对全局暴露，因此内层函数中不应声明要隐藏的变量，而内层函数还要能访问到局部作用域中的变量，因此就需要能够通过作用域链查找变量。内层函数对外暴露是最终目的，所以并非必须使用return和作为参数两种方法，只要能暴露给全局，任何方法都可以，例如对window对象赋值：window.fn = fn，使fn成为全局。
## 总结
严格上说，闭包应该同时满足以上三个条件：
1. 访问所在作用域（作用域链）
2. 函数嵌套（内外层函数）
3. 在所在作用域外被调用（return或作为参数）

满足三个条件的意义：
* 条件1和2的意义在于创建一个全局无法访问，但内层函数可以访问的局部作用域
* 条件3的意义是使内层函数暴露到全局，使得可以间接访问变量

# 闭包中常见的错误：在循环中创建闭包
在ES6引入let关键字前，在循环中创建闭包常会出现下面的问题：
```html
<p id="help">Helpful notes will appear here</p>
<p>E-mail: <input type="text" id="email" name="email"></p>
<p>Name: <input type="text" id="name" name="name"></p>
<p>Age: <input type="text" id="age" name="age"></p>
```
```javascript
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    };
  }
}

setupHelp();
```
在数组helpText中定义了三个帮助信息，每条信息都关联了一个html文件中input输入框的id，通过循环helpText，给每个input上添加了一个onfocus的事件处理函数，当光标聚焦到该输入框时，显示对应的帮助信息。

运行代码后发现并没有达到预期效果，光标聚焦在任何框都显示年龄的帮助信息。这是因为添加到onfocus的三个事件处理函数都共享了setupHelp的词法作用域，由于var关键字声明的变量没有块作用域且var声明的变量有提升机制，使得item变量也在setupHelp词法作用域中，故item变量也是被三个函数所共享，并没有分别绑定。当事件处理函数调用时，for循环已经执行完毕，item变量的值为helpText[2]，三个事件处理函数都是使用这个item值做为参数传入showHelp函数，就导致绑定了相同的帮助信息。

看到这里，我们就可以针对问题，给出解决方案了。

问题：事件处理函数共享上层词法作用域中的同一个变量

解决：为事件处理函数创建彼此独立的词法作用域，使之对应正确的变量
## 解决方案一 多重函数闭包
在出问题的函数外再包裹一个函数并形成闭包，为事件处理函数新创建一层彼此独立的词法作用域。
```javascript
function showHelp(help) {
    document.getElementById("help").innerHTML = help;
}
function makeHelpCallback(help) {
    return function() {  //这里通过return的方式形成了匿名函数对makeHelpCallback作用域的闭包
        showHelp(help);
    };
}
function setupHelp() {
    var helpText = [
        {"id": "email", "help": "Your e-mail address"},
        {"id": "name", "help": "Your full name"},
        {"id": "age", "help": "Your age (you must be over 16)"}
    ];
  
    for (var i = 0; i < helpText.length; i++) {
        var item = helpText[i];
        document.getElementById(item.id).onfocus = makeHelpCallback(item.help);
    }
}

setupHelp();
```
虽然setupHelp函数作用域中item的值仍然是固定的，但是在多重函数闭包后，增加了makeHelpCallback函数的作用域，正确的item.help在for循环时被当作makHelpCallback函数的参数，添加到了makeHelpCallback函数的作用域中，并通过闭包保留了下来。事件处理函数调用时，showHelp函数从makeHelpCallback函数的作用域中获得正确的item.help值做为参数，就可以绑定正确的帮助信息了。
## 解决方案二 IIFE
通过使用立即调用函数表达式，立即调用时封闭当前作用域，使每次for循环都形成一个独立的词法作用域，防止了var声明没有块作用域且有变量提升的特性。
```javascript
function showHelp(help) {
    document.getElementById("help").innerHTML = help;
}
function setupHelp() {
    var helpText = [
        {"id": "email", "help": "Your e-mail address"},
        {"id": "name", "help": "Your full name"},
        {"id": "age", "help": "Your age (you must be over 16)"}
    ];
  
    for (var i = 0; i < helpText.length; i++) {
        (function () {
            var item = helpText[i];
            document.getElementById(item.id).onfocus = function() {
                showHelp(item.help);
            };
        })();
    }
}
  
setupHelp();
```
IIFE将每次for循环后的item变量分别封闭在三个IIFE的作用域中，item不再存在于三个函数共享的setupHelp作用域了。这样showhelp函数调用时以IIFE作用域中的item值为参数，也可以绑定正确的帮助信息。
## 解决方案三 let
既然一切都是因为var没有块作用域且有变量提升机制造成的，那么我们使用ES6新增的关键字let就可以完美解决这个问题了。
```javascript
function showHelp(help) {
    document.getElementById("help").innerHTML = help;
}
function setupHelp() {
    var helpText = [
        {"id": "email", "help": "Your e-mail address"},
        {"id": "name", "help": "Your full name"},
        {"id": "age", "help": "Your age (you must be over 16)"}
    ];
  
    for (var i = 0; i < helpText.length; i++) {
        let item = helpText[i];
        document.getElementById(item.id).onfocus = function() {
            showHelp(item.help);
        };
        
    }
}
  
setupHelp();
```
注意：ES6中块并不会创建新的执行环境和作用域，但代码运行到块时，会用块的词法作用域代替当前执行环境的词法作用域，直到块中代码运行完毕，再用原执行环境的词法作用域取代块的词法作用域。

因此用let声明item后，每次for循环都会创建一个独立的块作用域，事件处理函数调用时，虽然执行环境都是setupHelp执行环境，但块作用域是不同的，事件处理函数也会在彼此独立的块作用域中查找item变量做为参数，这样就实现了item.help的正确绑定。