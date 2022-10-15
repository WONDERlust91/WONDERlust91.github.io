---
title: javascript监听浏览器tab页关闭事件
date: 2021-05-06 17:51:03
tags:
  - beforeunload
  - 浏览器关闭事件
categories: javascript
---

## 如何监听浏览器关闭事件

```js
window.addEventListener("beforeunload", (event) => {
  // ...your code here
});
```

这个事件在窗口即将卸载时调用，此时文档依然可见，事件也可取消

<!-- more -->

## 注意事项

1. 为了对抗扰人的弹窗，一些浏览器将不会显示创建在 beforeunload 事件中的提示窗口，除非用户与这个页面有过交互操作。还有些浏览器完全禁止在 beforeunload 事件下的任何弹窗。**这些弹窗指开发人员新创建的弹窗，而不包括取消该事件时的默认提示窗。**

2. 不该在 beforeunload 事件中使用 Navigator.sendBeacon()方法（该方法通常用来传递网站的分析数据），因为 beforeunload 等事件在浏览器前进/后退缓存（back/forward cache 或称 bfcache）或移动端情况下各浏览器行为不一致，并不能保证触发。可以在 visibilityChange 事件或 pageHide 事件中使用 Navigator.sendBeacon()方法。

## 弹窗提示用户，取消该事件

HTML 规范要求开发人员使用 Event.preventDefault()方法来弹窗取消事件，而不是使用 Event.returnValue。但这还没有被所有浏览器所支持。

当事件的回调函数返回值（或 e.returnValue 的值）不为 null 或 undefined 时，将会要求用户在提示框中确认页面卸载。在旧的浏览器中，返回值的字符串将会显示在弹窗中，而从 Firefox 44，Chrome 51，Opera 38，Safari 9.1 开始，通用的提示语将显示在弹窗中，而不是返回值。IE 并未识别 null 值，并会在弹窗中展示 null 的字符串，在 IE 中需要使用 undefined 来跳过弹窗。

在一些浏览器中，在 beforeunload 事件下调用 window.alert()，window.confirm()，window.prompt()会被忽略。

还需要注意的是，大量的浏览器都会忽略事件的结果，并不向用户确认就自动关闭。例如 Firefox 设置中有一个 dom.disable_beforeunload 功能，开启后不会弹窗直接关闭。而在 Chrome 60 后，如果用户与页面没有任何交互，那么关闭也不会有弹窗确认。在 Chrome 81 后，开启开发者工具的情况下，按下 F5 与点击刷新按钮也不计入用户交互。

```js
window.addEventListener("beforeunload", function (e) {
  // 取消事件，Firefox将在调用取消事件后弹窗
  e.preventDefault();
  // Chrome则需要设置returnValue
  e.returnValue = "";
});
```
