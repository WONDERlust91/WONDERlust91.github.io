---
title: javascript事件回调参数
date: 2018-12-06 13:30:06
tags:
- javascript
- 事件回调参数
categories: javascript
---

# javascript的事件回调参数

```javascript
const ele = document.getElementById("id");
function haha(abc) {
    console.log(abc);
    console.log(event);
}

// 声明一个参数，则该参数为事件，同时也可以使用默认事件变量event
ele.addEventListener("click", e => {
    console.log(e); // event
    console.log(event) // event
}, true);

// 不声明参数，则默认传入event
ele.addEventListener("click", () => {
    console.log(event); // event
}, true);

// 声明一个命名函数，也是同样道理
ele.addEventListener("click", function haha() {
    console.log(event); // event
}, true);


ele.addEventListener("click", function haha(abc) {
    console.log(abc); // event
    console.log(event); // event
}, true);

ele.addEventListener("click", haha); // event event

// 相当于页面加载时直接执行haha函数，并不是在点击时调用，此时event一定为undefined，参数abc根据是否传入而定，若不传则为undefined，否则为传入的值本身。
ele.addEventListener("click", haha()); // undefined undefined

```

通过上面的代码我们可以看出，在javascript中使用addEventLister监听事件，并不能给回调函数携带参数，只能自定义event的变量名，或者使用默认的event变量。默认的event变量是自动传入回调函数的。

```javascript
function haha(abc) {
    console.log(abc);
    console.log(event);
}
```

```html
<!-- 不传参数时可以取到默认的event， -->
<div onclick="haha()"></div>
<!-- undefined event -->

<!-- 传了参数可以取到参数与默认的event -->
<div onclick="haha('haha')"></div>
<!-- haha event -->
```

在html中监听事件，可以传递参数，event依然默认传入函数中，若声明函数参数时，声明了和event同名的参数，则要将实参event传入函数，否则打印event报undefined。

```javascript
// 声明形参event
function haha(abc, event) {
    console.log(abc);
    console.log(event);
}
```

```html
<!-- 不传参数时无法取到默认的event， -->
<div onclick="haha()"></div>
<!-- undefined undefined -->

<!-- 传了参数可以取到参数与event -->
<div onclick="haha('haha', event)"></div>
<!-- haha event -->
```

vue中的事件与使用html属性监听事件相同，若声明了event形参，则应该传入$event实参，否则报undefined。