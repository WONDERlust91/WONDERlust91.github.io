---
title: javascript关键字this
date: 2018-03-15 15:10:22
tags: this
categories: javascript
---
# this的四种绑定（动态作用域，动态绑定，绑定调用时的对象）
## 一、this的默认绑定
### 全局上下文中（即在任何函数体外部）
无论是否为严格模式，this均绑定到全局对象（node中为global，浏览器中是window）。
```javascript
"use strict"
console.log(this === window); // true
```
### 在函数上下文中
#### (1)严格模式下，this默认绑定为undefined
```javascript
function foo() {
    "use strict";
    console.log(this === undefined);
}
foo(); // true
```
#### (2)非严格模式下，this默认绑定到全局（node中是global，浏览器中是window）有以下四种情况：
<!-- more -->
##### 1.函数独立调用
```javascript
function foo() {
    console.log(this === window);
}
foo(); // true
```
##### 2.被嵌套的函数独立调用
```javascript
const obj = {
    foo() {    //ES6中对象的语法糖，原写法为 foo: function () {...
        function bar () {
            console.log(this === window);
        }
        bar();
    }
};
obj.foo(); // true
```
##### 3.IIFE立即执行函数
```javascript
const obj = {
    foo() {
        (function bar() {
            console.log(this === window);
        })();
    }
};
obj.foo(); // true
```
##### 4.闭包
```javascript
const obj = {
    foo() {
        function bar() {
            console.log(this === window);
        }
        return bar;
    }
};
obj.foo()(); // true    obj.foo()相当于bar，obj.foo()()相当于bar();
```
由于闭包使this绑定到全局，而我们常常要访问上层嵌套函数的this，通常我们在上层嵌套函数中把this赋值给that，在闭包中使用that即可访问到嵌套函数中的this。（也可以用ES6的箭头函数来实现，见后文）
```javascript
const obj = {
    foo() {
        const that = this;
        function bar() {
            console.log(that === obj);
        }
        return bar;
    }
};
obj.foo()(); // true
```
用箭头函数实现
```javascript
const obj = {
    foo() {
        const bar = () => console.log(this === obj);
        return bar;
    }
};
obj.foo()(); // true
```
## 二、this的隐式绑定与丢失
### 隐式绑定  
被直接对象所包含的函数调用时，即函数的方法调用，this隐式绑定到该直接对象。在多重对象中，this绑定到最近一层的对象。
```javascript
function foo() {
    console.log(this.objName);
}
const obj1 = {
    objName: "obj1",
    foo: foo,
    obj2: {
        objName: "obj2",
        foo: foo,
    }
};
obj1.foo(); // obj 1
obj1.obj2.foo(); // obj2
```
### 隐式丢失  
this的隐式丢失是指：被隐式绑定的函数丢失绑定的对象，从而默认绑定到全局对象（window或global）。
this的隐式绑定由对象的方法函数是如何被调用决定，而不是由函数定义在哪个对象决定。故会出现this的隐式丢失。
this的隐式丢失的基本原理都是间接引用了对象的方法函数，对象只是作为函数引用的桥梁，函数并没有通过对象调用。（更底层的解释是对象和对象的方法函数存储在不同的内存位置，只有通过对象的内存调用对象方法函数的内存时，才能实现隐式绑定，间接引用时相当于只调用了对象方法函数的内存，故造成了隐式丢失，this默认绑定到了全局。）

隐式丢失的四种情况：
#### 1.对象的方法函数赋值给其他变量、常量(相当于默认绑定中的独立调用)
```javascript
function foo() {
    console.log(this.objName);
}
const obj = {
    objName: "obj",
    foo: foo,
};
const bar = obj.foo;
bar(); // undefined
```
网上很多例子中喜欢在全局中var objName = "window"，使this绑定到全局时this.objName显示为"window"。但是在ES6中，使用let或const objName = "window"，this绑定到全局时this.objName为undefined。这是因为var声明的全局变量会自动挂载到顶层对象window中，除了可以直接访问，还可以通过window属性访问，而let与const声明的变量只能通过变量名直接访问。
#### 2.对象的方法函数赋值给其他对象后立即执行（相当于默认绑定中的IIFE）    
```javascript
function foo() {
    console.log(this.objName);
}
const obj1 = {
    objName: "obj1",
    foo: foo,
};
const obj2 = {
    objName: "obj2",
};
(obj2.foo = obj1.foo)(); // this.objName为undefined，因为this指向全局对象window，其中没有objName属性
obj2.foo(); // obj2
```
#### 3.对象的方法函数经过赋值或运算后再执行（相当于默认绑定中的独立调用）
```javascript
function foo() {
    console.log(this.objName);
}
const obj = {
    objName: "obj",
    foo: foo,
};
(obj.foo = obj.foo)(); // this.objName为undefined
(false || obj.foo)(); // this.objName为undefined
(1, obj.foo)(); // this.objName为undefined
```
#### 4.对象的方法函数作为其他函数的参数传递（相当于默认绑定中的独立调用）
不论对象的方法函数是作为内置函数还是声明函数的参数，都会隐式丢失。
```javascript
function foo() {
    console.log(this.objName);
}
function bar(fn) {
    fn();
}
const obj = {
    objName: "obj",
    foo: foo,
};
bar(obj.foo); // this.objName为undefined
setTimeout(obj.foo, 100); // this.objName为undefined
```
## 三、this的显式绑定
### 非永久绑定：call、apply
fn.call(obj, arg1, arg2, ...argn) 被调用的参数依次给出
fn.apply(obj, [arg1, arg2, ...argn]) 被调用的参数以数组的形式给出
call、apply作用：
1. 即刻调用函数fn
2. 调用函数fn时，函数fn的this绑定到obj
```javascript
function foo() {
    console.log(this.objName);
}
const obj = {
    objName: "obj",
};
foo.call(obj); // obj
```
### 永久绑定：硬绑定、bind
硬绑定：在call或apply函数外包装一个函数，使用时直接调用外层函数，以达到永久绑定的效果。
```javascript
function foo() {
    console.log(this.objName);
}
const obj = {
    objName: "obj",
};
function bar() {
    foo.apply(obj);
}
bar(); // obj
```
bind方法：返回一个指定了执行上下文的新函数，且新函数能接收参数，bind方法中的参数也会被永久固定。

用bind方法代替硬绑定
```javascript
function foo() {
    console.log(this.objName);
}
const obj = {
    objName: "obj",
}
const bar = foo.bind(obj);
bar(); // obj
```
bind方法固定参数
```javascript
function foo(num1, num2) {
    console.log(this.num + num1 + num2);
}
const obj = {
    num: 1,
};
const bar1 = foo.bind(obj);
bar1(2, 3); // 6 只绑定了对象，参数可自行指定
const bar2 = foo.bind(obj, 1);
bar2(2); // 4 绑定了一个参数，剩余一个参数可自行指定
const bar3 = foo.bind(obj, 1, 1);
bar3(2, 3); // 3 绑定了两个参数，自行指定参数无效
const bar4 = foo.bind(obj, 1, 1, 1);
bar4(2, 3); // 3 多余绑定的一个参数被丢弃，只有两个参数生效,自行指定参数无效
```
非严格模式下使用call、apply、bind方法时，传入null、undefined作为第一个参数，this会被转换为全局对象（window或global）。
严格模式下传入null为null，传入undefined为undefined
```javascript
function foo() {
    console.log(this);
}
function bar() {
    "use strict";
    console.log(this);
}
const obj = {
    objName: "obj",
};
const foo1 = foo.bind(obj);
foo1(); // obj对象
const foo2 = foo.bind(null);
foo2(); // window对象
const bar1 = bar.bind(null);
bar1(); // null
```
## 四、this的new绑定
当一个函数用作构造函数时，this指向新构造的对象。

虽然构造器默认返回this指向的新对象，但是也可以手动返回其他对象（如果返回值不是一个对象，则返回this指向的新对象）。
```javascript
function foo1() {
    this.objName = "obj1";
}
function foo2() {
    this.objName = "obj2";
    return; // 返回空或非对象值，返回this指向的新对象
}
function foo3() {
    this.objName = "obj3";
    return {objName: "obj4"};
}
const bar1 = new foo1();
const bar2 = new foo2();
const bar3 = new foo3();
console.log(bar1, bar2, bar3); 
// {objName: "obj1"}, {objName: "obj2"}, {objName: "obj4"}
```
# this四种绑定的优先级
new>显式>隐式>默认
```javascript
function foo(Name) {
    if (Name) {
        this.objName = Name;
    }
    console.log(this.objName);
}
const obj1 = {
    objName: "obj1",
    foo: foo,
};
const obj2 = {
    objName: "obj2",
};
obj1.foo(); // obj1 隐式绑定优先于默认绑定
obj1.foo.call(obj2); // obj2 显式绑定优先于隐式绑定
const bar = obj1.foo.bind(obj2); 
const bar1 = new bar("bar1"); // bar1 new绑定优先于显式绑定
```
# ES6中箭头函数的this绑定（静态作用域，静态绑定，绑定定义时的对象）
箭头函数简化语法方式：
1. 可以省略function这个单词
2. 如果函数只有一个参数，则可以省略圆括号（没有参数和多个参数时不能省略）
3. 如果函数体是一个表达式，则可以省略花括号和返回语句
```javascript
const fn1 = () => "hello world!";
// 等价于 const fn1 = function () {return "hello world!";}
const fn2 = name => `Hello ${name}!`;
// 等价于 const fn2 = function (name) {return `Hello ${name}`;}
const fn3 = (a, b) => a + b;
// 等价于 const fn3 = function (a, b) {return a + b;}
const fn4 = () => ({a: 0, b: 1})
// 如果返回值是一个对象，应加一层圆括号，因为js会将对象的花括号当作代码块的花括号
```
箭头函数中this遵循词法作用域（静态作用域），会继承外层函数的this绑定，且无法通过显式绑定来修改绑定的对象。
```javascript
function foo() {
    return () => console.log(this.objName); 
}
const obj = {
    objName: "obj",
    foo: foo, // 这里foo函数的this先通过动态的隐式绑定绑定到了obj上，foo中的箭头函数又通过词法作用域的静态绑定继承了foo中隐式绑定到obj的this。
};
const bar = obj.foo();
bar(); // obj
bar.call({objName: "haha"}); // 显式绑定无效，还是obj
```
**箭头函数本身没有this、arguments等变量，它的this与arguments都继承于父作用域。箭头函数不能当对象构造器，即不能使用new。指定的参数变量argument在箭头函数中无效，获取剩余参数应使用...rest方法。**
常见错误是在对象中直接使用箭头函数，因为对象本身不构成作用域，所以对象中直接使用的箭头函数在对象中继承不到this，继续向上到达全局，故this没有指向obj而指向了window。故一般使用箭头函数时应该在外部嵌套一层普通函数。
```javascript
obj = {
    objName: "obj",
    foo: () => console.log(this),
};
obj.foo(); // 结果为window而不是obj
```
