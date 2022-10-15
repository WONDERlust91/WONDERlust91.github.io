---
title: javascript检测DOM尺寸变化的API-ResizeObserver
date: 2021-04-05 18:43:47
tags:
  - javascript
  - ResizeObserver
categories: javascript
---

## 背景介绍

DOM 元素的属性或节点变化的监测，可以使用 MutationObserver 对象，但若想要检测 DOM 元素尺寸变化，过去是没有可以直接使用的 API 的，多是借助 window 对象上绑定 resize 事件。

但这种方式是有缺陷的，有时候 DOM 元素尺寸变化了，但 window 尺寸并没有变化，就监听不到；还有时候 window 尺寸变化了，但 DOM 元素尺寸并没有变化，在 window 上绑定 resize 事件就浪费性能。

基于这样的使用背景，就出现了新的 API ResizeObserver。这个 API 使用了观察者模式，专门用来监听：

1. 某个节点的出现和隐藏；

2. 某个节点的尺寸变化；

该 API 较新，chrome、firefox 最新版均已支持，而 safari 也确认未来会支持。使用 polyfill 可以兼容至 IE11。

<!-- more -->

## API 语法

ResizeObserver 接口可以监听到 Element 的**内容区域**（参考盒模型的内容区域，即不包含 margin、padding、border 的纯 content box 区域）或 SVGElement 的**边界框**改变。

ResizeObserver 避免了在自身回调中调整大小，从而触发的无限回调和循环依赖。它仅通过在后续帧中处理 DOM 中更深层次的元素来避免这一点。若浏览器实现遵循规范，则只会在绘制前或布局后触发调用。

### 构造器

ResizeObserver() 创建并返回一个 ResizeObserver 对象

```javascript
const ResizeObserverObj = new ResizeObserver(callback);
```

callback：当观察到元素尺寸变化时调用该函数，函数有两个参数

- entries：ResizeObserverEntry 对象的 Array，每个尺寸发生变化的被监听元素都会生成一个 ResizeObserverEntry 对象。ResizeObserverEntry 对象包含以下属性：
  - borderBoxSize：观察元素边界区域尺寸对象，属性如下：
    - inlineSize：表示内联元素排列方向上尺寸，等同于 CSS 逻辑属性 inline-size，在默认文档流下表示宽度
    - blockSize：表示块级元素排列方向上尺寸，等同于 CSS 逻辑属性 block-size，在默认文档流下表示高度
  - contentBoxSize：观察元素内容区域尺寸对象，属性同上
  - devicePixelContentBoxSize：观察元素内容区域在设备像素下的尺寸对象，属性同上
  - contentRect：观察元素变化后的尺寸对象，相比前三个属性这个属性有更好的兼容性，但这个属性是早期实现的版本，在未来的版本中可能不推荐使用，属性如下
    - x
    - y
    - width
    - height
    - top
    - right
    - bottom
    - left
  - target：观察元素本身
- observer：ResizeObserver 对象本身，例如可以在达到特定条件时自动取消观察，若不需要该参数，可忽略

### 方法

- ResizeObserver.disconnect() 取消和结束目标对象上所有对 Element 或 SVGElement 观察
- ResizeObserver.observe() 开始观察指定的 Element 或 SVGElement
- ResizeObserver.unobserve() 结束观察指定的 Element 或 SVGElement

### 示例

下面示例展示了 box 调整大小时，其内部文本显示为尺寸大小

```javascript
const resizeObserver = new ResizeObserver((entries) => {
  entries.forEach((entry) => {
    entry.target.textContent = `${entry.contentRect.width} X ${entry.contentRect.height}`;
  });
});

resizeObserver.observe(document.querySelector(".example"));
```

## Polyfill

Polyfill 项目地址：[https://github.com/juggle/resize-observer]，兼容至 IE11

## Typescript 中使用遇到的问题

由于 Typescript 官方还未将 ResizeObserver 放入 lib.dom.ts 中，我们在 Typescript 中使用该 API 会报类型错误，故需要安装 @types/resize-observer-browser。

更多信息请参考[github issue](https://github.com/Microsoft/TypeScript/issues/28502)

> 参考资料
>
> [张鑫旭-检测 DOM 尺寸变化 JS API ResizeObserver 简介](https://www.zhangxinxu.com/wordpress/2020/03/dom-resize-api-resizeobserver/)
>
> [凯斯-ResizeObserver API](https://zhuanlan.zhihu.com/p/41418813)
>
> [MDN-ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver)
