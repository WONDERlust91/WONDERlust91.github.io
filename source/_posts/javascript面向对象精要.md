---
title: javascript面向对象精要
date: 2021-02-20 13:12:09
tags: 面向对象
categories: javascript
---

## 原始类型与引用类型

JavaScript 虽然没有类的概念（ES6 后增加了 class 语法糖，但本质上还是通过原型实现的），但依然存在两种类型：原始类型和引用类型。

- 原始类型保存为简单数据值；
- 引用类型则保存为对象，其本质是指向内存位置的引用；

为了让开发者能够把原始类型和引用类型按相同方式处理，JavaScript 花费了很大努力来保证语言的一致性。

其他编程语言用栈储存原始类型，用堆储存引用类型。JavaScript 则完全不同：它使用一个变量对象追踪变量的生命周期，原始类型的值被直接保存在变量对象内，而引用类型的值则作为一个指针保存在变量对象内，该指针指向实际对象在内存中的存储位置。

### 原始类型

JavaScript 共有 5 种原始类型：boolean number string null undefined。所有原始类型的值都有字面形式。原始类型的变量直接保存原始值。

鉴别原始类型的最佳方法是使用 typeof 操作符。它可以被用在任何变量上，并返回一个说明数据类型的字符串。

需要注意的是当运行 typeof null 时，结果是'object'，其实这已经被设计和维护 JavaScript 的委员会 TC39 认定是一个错误，但出于向前兼容的需要，并未修改。在逻辑上可以认为 null 是一个空的对象指针，所以结果为'object'。判断一个值是否为空类型的**最佳**方法是直接和 null 比较`value === null`。

> 非强制转换比较  
> === 在进行比较时不会将变量强制转换为另一种类型，所以比较 undefined 和 null 时，== 认为它们相等，而 === 认为它们不相等。

虽然字符串、数字、布尔是原始类型，并不是对象，但它们也拥有方法（null 和 undefined 没有方法）。JavaScript 使它们看上去像对象一样，以此来提供语言上的一致性体验。

### 引用类型

引用类型主要指 JavaScript 中的对象，同时也是该语言中最接近类的东西。我们既可以使用 new 操作符和构造函数的方式来创建对象，也可以使用字面量的形式创建对象。任何函数都可以是构造函数，根据命名规范，JavaScript 中的构造函数用首字母大写跟非构造函数进行区分。

引用类型不在变量中直接保存对象实例，而是一个指向内存中实际对象所在位置的指针（或者说引用）。当一个对象赋值给变量时，实际是赋值给这个变量一个指针。这意味着将一个变量赋值给另一个变量时，两个变量各获得了一份指针的拷贝，指向内存中的同一个对象。

> 参考资料
>
> [leaflet 加载 arcgis 切片](https://www.jianshu.com/p/85df27410fb8/)
>
> [LeaFlet 中切片图层使用自定义坐标系](https://blog.csdn.net/weixin_40184249/article/details/83933048)
>
> [leaflet 系列二 加载 arcgis 自定义坐标系服务](https://blog.csdn.net/u010303603/article/details/81741630)
>
> [在 Leaflet 中自定义 4490 坐标系](https://blog.csdn.net/yisimo/article/details/109388789)
>
> [EPSG Geodetic Parameter Dataset](https://en.wikipedia.org/wiki/EPSG_Geodetic_Parameter_Dataset)
