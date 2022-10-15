---
title: javascript原型与原型链及继承
date: 2018-05-02 12:29:41
tags:
categories: javascript
---

## 原型链与继承

![__proto__ and prototype](/images/relationship_between_function_and_prototype.png)
这张图可以说是一图读懂原型链与继承了。这张图在原来的基础上加入了 es6 中的 class 语法，class 只是语法糖，本质上还是构造函数。

<!-- more -->

学习原型链与继承前，首先要搞懂三个基本点：

1. 在 js 中，万物皆对象，函数（Function）是对象，函数的原型（Function.prototype）是对象，它们都会有对象的共同特点。
2. 对象都有\_\_proto\_\_属性，指向该对象构造函数的原型对象。原型链也是通过\_\_proto\_\_属性实现的。
3. 函数是一种特殊的对象，除了对象都有的\_\_proto\_\_属性外，还有一个 prototype 属性，该属性指向函数的原型对象，原型对象中又有一个 constructor 属性，又指回了构造函数。

接下来就讲解一下这张图：

### Foo()（class Foo {}）

构造函数 Foo：有一个属性 prototype 指向其原型对象，原型对象中有 f1、f2 等等所有实例可以共享的方法。Foo 还有个\_\_proto\_\_属性指向其构造函数的原型对象 Function.prototype。（所有函数的构造函数是 Function 函数，其原型对象则为 Function.prototype）

原型对象 Foo.prototype：又有一个 constructor 属性，constructor 指向构造函数 Foo 本身。原型对象 Foo.prototype 也是对象，也有对象都有的属性\_\_proto\_\_，指向其构造函数 Object 的原型对象 Object.prototype。（所有对象的构造函数是 Object 函数，其原型对象为 Object.prototype)

实例对象 f1、f2：也有属性\_\_proto\_\_，指向构造函数 Foo 的原型对象 Foo.prototype，这样就可以访问原型对象中共享的所有方法了。

### class Bar extends Foo {}

这是 ES6 的语法糖，创建了一个 Foo 的子类，本质上还是构造函数，与 ES5 中通过 Foo 函数使用 Object.creat()等方法创建的子类相类似。Bar 继承了 Foo 的方法与属性。

构造函数 Bar：属性 prototype 指向其原型对象，\_\_proto\_\_属性则与一般的函数不同，指向父类的构造函数 Foo，而不是函数构造函数的原型对象 Function.prototype。

原型对象 Bar.prototype：constructor 属性指向构造函数 Bar 本身。\_\_proto\_\_属性也和一般原型对象不同，指向父类构造函数的原型对象 Foo.prototype，而不是对象构造函数的原型对象 Object.prototype。

实例对象 b1、b2：属性\_\_proto\_\_指向构造函数 Bar 的原型对象 Bar.prototype。

### Function()

构造函数 Function：prototype 属性指向其原型对象 Function.prototype。\_\_proto\_\_属性指向其构造函数的原型对象，由于 Function 的构造函数就是自己，所以\_\_proto\_\_属性也指向自己的原型对象 Function.prototype。

原型对象 Function.prototype：constructor 属性指向构造函数 Function 本身。\_\_proto\_\_属性指向构造函数的原型对象 Object.prototype。因为原型对象也是对象，对象的构造函数是 Object，Object 的原型对象为 Object.prototype。

### Object()

构造函数 Object：prototype 属性指向其原型对象 Object.prototype。\_\_proto\_\_属性指向其构造函数 Function 的原型对象 Function.prototype。

原型对象 Object.prototype：constructor 属性指向构造函数 Object 本身。\_\_proto\_\_属性指向 null。因为万物皆对象，对象的原型对象已经是最末端了，所以\_\_proto\_\_对象指向 null。

实例对象 o1、o2：不论是用对象字面量还是构造函数生成的对象，\_\_proto\_\_属性都指向其构造函数 Object 的原型对象 Object.prototype。

[注释]: 由于双下划线在markdown中是加粗，所以在文中用反斜杠反转义

### 原型链

由原型对象相互关联，组成的链状结构就是原型链。在图中可以直观看到所有\_\_proto\_\_的虚线构成了一条条的原型链。有了原型链，当试图访问一个对象的属性时，不仅仅在对象本身中查找，还会沿着原型链查找对象的原型对象，依次层层向上，直到找到一个名称匹配的属性或到达原型链的末尾，也就是 Object.prototype.\_\_proto\_\_即 null。

### 继承

对象都会从原型对象上继承属性和方法，这里继承并不是复制，而是通过\_\_proto\_\_在对象与原型对象间创建关联，一个对象通过这样的关联关系就可以访问另一个对象的属性与方法。

### \_\_proto\_\_与 prototype 的作用

prototype 所指向的原型对象是用来被其他对象继承的，而\_\_proto\_\_属性是用来保存从原型对象继承的索引关系的。

## \_\_proto\_\_的由来

说到\_\_proto\_\_的由来，就不能不提[[prototype]]属性。[[prototype]]属性是所有对象都有的私有属性（内置属性），在 ES5 以前没有原生的方法去访问[[prototype]]这个属性，于是各个浏览器厂商就定义了\_\_proto\_\_来读写[[prototype]]这个私有属性，本质上是一个访问器属性（一个 getter 函数和一个 setter 函数）。而直接读写[[prototype]]容易造成 bug 与性能问题，因此官方并不建议使用\_\_proto\_\_。在 ES5 中，官方加入了内置属性标准 Get 方法，Object.getPrototypeOf()。在 ES6 中，官方又加入了内置属性标准的 Set 方法，Object.setPrototypeOf()。

有趣的是\_\_proto\_\_属性也是一例典型的实践倒逼准则的案例，由于各大浏览器厂商对\_\_proto\_\_属性的支持，使该属性也被 ES6 的标准所囊括。

## \_\_proto\_\_的指向

根据前面讲到的，我们知道一个对象的\_\_proto\_\_属性通常指向其构造函数的原型对象。因此确定对象\_\_proto\_\_指向的关键点就在于找到其构造函数。接下来通过 4 种不同的构造对象的方法，来依次分析\_\_proto\_\_的指向。

### （1）对象字面量

对象字面量就是直接声明对象的方式。

```javascript
const obj1 = {
  objName: 'obj1'
};
```

事实上，对象字面量只是一种语法糖，本质上还是通过 new Object()的方式构造了对象。

```javascript
// 上例本质上等价于
const obj1 = new Object();
obj1.objName = 'obj1';
```

因此，通过对象字面量构造出的对象的\_\_proto\_\_属性都指向构造函数 Object 的原型对象 Object.prototype

### （2）new 关键字

如果通过 new 关键字创建一个普通的对象（同上例），则对象的\_\_proto\_\_属性同上，指向 Object.prototype。

如果用自定义的构造函数（如 Foo）通过 new 关键字创建实例对象，则对象的\_\_proto\_\_属性指向那个自定义构造函数的原型对象（如 Foo.prototype）。

new 实现过程大致如下

```javascript
// 例如new Object()相当于
(function() {
  const obj = {};
  obj.__proto__ = Object.prototype;
  Object.apply(obj, arguments);
  return obj;
})();
// 再例const f = new Foo()相当于
const f = (function() {
  const obj = {};
  obj.__proto__ = Foo.prototype;
  Foo.apply(obj, arguments);
  return obj;
})();
```

### （3）Object.create(proto)

通过 Object.create 方法创建的对象，其\_\_proto\_\_属性指向该方法的第一个参数 proto。（create 方法还有别的参数，这里不涉及）

上面是结论，我们分析一下 Object.create 方法的实现方式，再来看其本质。

```javascript
function create(proto) {
  function F() {}
  F.prototype = proto;
  return new F();
}
```

可以看到 create 方法的实现本质上也是用了 new 关键字，不同的是 create 方法创造出的对象没有构造函数 constructor。没有构造函数只是说我们在外部无法访问构造函数，但函数运行过程中还是存在的，运行完后被销毁了。

```javascript
// 假设新建对象为f
const f = Object.create(proto);
// 在create内部上一行实质相当于
const f = new F();
// 按照new关键字构造对象的__proto__属性指向的基本原则,
// 对象f的__proto__属性指向构造函数F的原型对象F.prototype
f.__proto__ === F.prototype; //true
// 而在create方法的实现中F.prototype被赋值为传入的参数proto
F.prototype === proto; //true
// 则得出
f.__proto__ === proto;
```

由此看出 Object.create 方法构造出的对象的\_\_proto\_\_属性指向第一个参数 proto。

Object.create 方法与 new 关键字在功能上是基本相同的，Object.create 优点是对函数与对象都可以使用，而 new 只能对函数使用。缺点是 Object.create 与 new 相比性能较差。

### （4）class

class 是 ES6 新增的模仿类的语法，其本质上还是基于原型的构造函数，class 只是语法糖。通过 class 构造函数 new 的实例，实例的\_\_proto\_\_属性指向 class 构造函数的原型对象。

```javascript
class Foo {
  constructor() {}
}
const foo = new Foo();
foo.__proto__ === Foo.prototype; // true
```

类中最重要的是继承，值得注意的是子类的\_\_proto\_\_属性指向与一般情况不同。子类构造函数的\_\_proto\_\_属性指向父类构造函数，子类构造函数的原型对象的\_\_proto\_\_属性指向父类构造函数的原型对象。

```javascript
class Bar extends Foo {
  constructor() {
    super();
  }
}
const bar = new Bar();
bar.__proto__ === Bar.prototype; // true
Bar.__proto__ === Foo; // true
Bar.prototype.__proto__ === Foo.prototype; // true

// 子类的继承实现大致如下，故会出现与一般情况不同的__proto__指向
Object.setPrototypeOf(Bar.prototype, Foo.prototype);
Object.setPrototypeOf(Bar, Foo);
```

class 在 ES6 中才出现，那么在 ES6 以前，如何实现父类与子类的继承关系呢？

```javascript
function Foo() {
  // 这是父类
}
function Bar() {
  // 这是子类
  Foo.apply(this, arguments); // 继承第一步，用Bar的this与参数调用Foo
}
Bar.prototype = new Foo(); // 继承第二步，改写Bar的原型，建立与Foo的原型链关系
// 也可以写成 Bar.prototype = Object.create(Foo.prototype);

// 由于Bar.prototype被赋值语句改写，因此原本的构造函数constructor与Bar.prototype的关系被切断了，
// Bar.prototype.constructor绑定到了Foo上，
// 需要再次绑定回Bar构造函数
Bar.prototype.constructor = Bar; // 继承第三步，绑定丢失的构造函数

const bar = new Bar();
bar.__proto__ === Bar.prototype; // true
Bar.__proto__ === Function.prototype; // true
Bar.prototype.__proto__ === Foo.prototype; // true
```

我们可以看出，ES5 与 ES6 在类的继承上几乎是相同的。除了语法糖造成的写法上的不同外，就是 Bar.\_\_proto\_\_属性指向不同。ES6 中 Bar.\_\_proto\_\_指向父类的构造函数。ES5 中则是指向原生函数的原型对象。

## 附加内容

### instanceof 的实现机制

instanceof 左值一般是对象，右值是构造函数。实现原理大致如下：

```javascript
L instanceof R
L.__proto__.__proto__... === R.prototype ? true : false
// 最终返回值是布尔值
// 结合文章开始的图，就会明白为什么下面的结果都是true了
Function instanceof Object; // true
Object instanceof Function; // true
Function instanceof Function; //true
Object instanceof Object; // true
```

### 实例的 constructor

尽管最终的实例对象并没有 constructor 属性，但仍然可以调用，这是为什么呢？其实只是通过原型链调用了原型上的 constructor 属性，并不是实例对象有 constructor 属性。

```javascript
function Foo() {}
const foo = new Foo();
foo.constructor === Foo; // true
foo.constructor === Foo.prototype.constructor; // true
```

> 参考链接  
> <https://www.zhihu.com/question/34183746>  
> <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain>  
> <https://github.com/mqyqingfeng/Blog/issues/2>
