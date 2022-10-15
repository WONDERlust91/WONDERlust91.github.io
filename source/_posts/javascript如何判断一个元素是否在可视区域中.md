---
title: javascript如何判断一个元素是否在可视区域中
date: 2021-08-22 15:24:15
tags:
  - javascript
  - 可视区域
  - offsetTop、scrollTop
  - getBoundingClientRect
  - intersection observer
categories: javascript
---

## 前置概念

### client 相关

| 名称         | 概念                                                                 | 备注                                                                                                                                                                                                      |
| ------------ | -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| clientWidth  | **可视区域**宽度，包含 padding，但并不包含 border、scrollbar、margin | 单位 px，只读，若元素大小超出可视区域，仅计算可视区域                                                                                                                                                     |
| clientHeight | **可视区域**高度，包含 padding，但并不包含 border、scrollbar、margin | 单位 px，只读，若元素大小超出可视区域，仅计算可视区域                                                                                                                                                     |
| clientTop    | 元素上边框宽度，不包含上部的 padding 与 margin                       | 单位 px，只读，与 borderTopWidth 属性相同                                                                                                                                                                 |
| clientLeft   | 元素左边框宽度，不包含左部的 padding 与 margin                       | 单位 px，只读，与 borderLeftWidth 属性相同，如果该元素有滚动条且 direction 为 right-to-left，在 windows 平台非 IE 浏览器该属性返回值为左边框宽度+滚动条宽度，IE 返回值为 0，在 macOS 平台均返回左边框宽度 |

<!-- more -->

### offset 相关

| 名称         | 概念                                                                                                                | 备注                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| offsetWidth  | **可视区域**宽度，包含 padding、border、scrollbar，但不包含 margin                                                  | 单位 px，只读，若元素大小超出可视区域，仅计算可视区域                                             |
| offsetHeight | **可视区域**高度，包含 padding、border、scrollbar，但不包含 margin                                                  | 单位 px，只读，若元素大小超出可视区域，仅计算可视区域                                             |
| offsetParent | 返回最近的 position 不为 static 的祖先元素                                                                          | 只读，若元素的 display 为'none'，则该属性为 null，该属性是 offsetTop、offsetLeft 的计算依据       |
| offsetTop    | 返回当前元素顶部相对父元素 offsetParent 顶部的距离，包含当前元素的 margin 及相对父元素的 padding、scrollbar、border | 只读，即当前元素 border 以外（不含 border）相对父元素 offsetParent border 以内（含 border）的距离 |
| offsetLeft   | 返回当前元素左侧相对父元素 offsetParent 左侧的距离，包含当前元素的 margin 及相对父元素的 padding、scrollbar、border | 只读，即当前元素 border 以外（不含 border）相对父元素 offsetParent border 以内（含 border）的距离 |

### scroll 相关

| 名称         | 概念                                                                | 备注                                                                                   |
| ------------ | ------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| scrollWidth  | 返回元素的全部宽度，包含 padding 但不包含 scrollbar、border、margin | 单位 px，只读，该属性包含了元素超出可视区域的部分                                      |
| scrollHeight | 返回元素的全部高度，包含 padding 但不包含 scrollbar、border、margin | 单位 px，只读，该属性包含了元素超出可视区域的部分                                      |
| scrollTop    | 设置或返回元素在垂直方向的滚动像素数                                | 如果设置值非法或元素不可滚动，该属性会被设置为 0，如果设置值超过最大值，则设置为最大值 |
| scrollLeft   | 设置或返回元素在水平方向的滚动像素数                                | 如果设置值非法或元素不可滚动，该属性会被设置为 0，如果设置值超过最大值，则设置为最大值 |

### getBoundingClientRect()

getBoundingClientRect 是元素上的一个方法，调用后返回元素的大小、相对视窗口的位置等信息。（每次滚动位置改变时，调用该方法的返回值都会变化）

返回的对象有 8 个属性：

| 名称   | 说明                                                                                |
| ------ | ----------------------------------------------------------------------------------- |
| x      | 元素原点（左上角顶点）的 x 坐标（相对视窗口左边的距离）                             |
| y      | 元素原点（左上角顶点）的 y 坐标（相对视窗口顶部的距离）                             |
| width  | 元素的宽度，包含 padding、scrollbar、border                                         |
| height | 元素的高度，包含 padding、scrollbar、border                                         |
| left   | 元素左边相对视窗口左边的距离，通常与 x 相同，若 width 是负值，则为 x + width 的值   |
| top    | 元素顶部相对视窗口顶部的距离，通常与 y 相同，若 height 为负值，则为 y + height 的值 |
| right  | 元素右边相对视窗口左边的距离，通常与 x + width 相同，若 width 为负值，则为 x 的值   |
| bottom | 元素底部相对视窗口顶部的距离，通常与 y + height 相同，若 height 为负值，则为 y 的值 |

### Intersection Observer API

Intersection Observer API 提供了一种异步检测目标元素与祖先元素或 viewport（视窗口）相交情况变化的方法。

过去，相交检测通常要用到事件监听，并且需要频繁调用`Element.getBoundingClientRect()`方法以获取相关元素边界信息。事件监听和调用`Element.getBoundingClientRect()`都是在主线程上运行，因此频繁触发、调用可能会造成性能问题。

假如一个无限滚动页面有很多动画，并使用了两个第三方库，这两个第三方库都有自己的相交检测程序，都运行在主线程中，当用户滚动页面时，这些相交检测程序就会在页面滚动回调函数里不停触发调用，造成性能问题，体验效果让人失望。

Intersection Observer API 会注册一个回调函数，每当被监视的元素进入或者退出另外一个元素时，或者两个元素的相交部分大小发生变化时，该回调方法会被触发执行。这样我们网站的主线程不需要再为了监听元素相交而辛苦劳作，浏览器会自行优化元素相交管理。

注意 Intersection observer API 无法提供重叠的像素个数或者具体哪个像素重叠，它的更常见的使用方式是——当两个元素相交比例在 N%左右时，触发回调，以执行某些逻辑。

#### 使用示例

Intersection Observer 允许配置一个回调函数，当以下情况发生时调用

- 每当目标（target）元素与设备视窗或其他指定元素发生交集的时候执行。设备视窗或其他元素我们称为根元素。

- Observer 第一次监听目标元素时执行。

目标元素（target）与根元素（root）之间的交叉程度叫交叉比（intersection ratio）。这是目标（target）元素相对于根（root）的交集百分比表示，它的取值在 0.0 到 1.0 之间。

```ts
// 传递给IntersectionObserver()构造函数的配置对象，控制观察者的回调函数的被调用时环境
const options = {
  // 指定根元素，用于检查目标元素可见性。必需是目标元素的祖先元素。如果未指定或为null，默认为浏览器视窗（viewport）
  root: document.querySelector("#container"),
  // 根元素外边距，用于确定root与target发生交集时计算交集的区域范围，使用该属性可控制root元素每一条边的收缩或扩张。如果指定了root，rootMargin也可以使用百分比来取值。
  rootMargin: "0px",
  // 触发的交叉比阈值，可以是单一的数字也可以是数字的数组，表示当target与root相交程度达到该值时回调会被执行。
  // 若要探测target在root中可见性超过50%时触发回调，则指定该属性为0.5；
  // 若要探测target在root中可见程度每多25%就触发一次回调，则指定该属性为[0, 0.25, 0.5, 0.75, 1]；
  // 默认值为0，表示target有一个像素出现在root中，就会触发回调执行；
  // 设置为1，表示target完全出现在root中的时候回调才会执行；
  threshold: 1.0,
};

// 回调函数
const callback = (entries, observer) => {
  entries.forEach((entry) => {
    // 每个entry都表示一个被观察的target元素的相交变化
    // entry.boundingClientRect target元素的位置、大小信息
    // entry.intersectionRatio target元素与root元素交叉比，实际计算的是entry.intersectionRect 面积 / boundingClinetRect 面积，元素非矩形时，也按矩形计算
    // entry.intersectionRect 相交区域的位置、大小信息
    // entry.isIntersecting target元素是否与root元素相交
    // entry.rootBounds root元素的位置、大小信息
    // entry.target target元素本身
    // entry.time 从observer的时间原点到交叉触发的时间的时间戳
  });
};

// 新建一个相交观察实例
const observer = new IntersectionObserver(callback, options);

// 监听target
observer.observe(document.querySelector("#element"));
```

## 判断元素在可视区域中

### 方法一 使用 scrollTop 与 offsetTop

```ts
/**
 * 通过元素及其可滚动的父元素，判断当前元素是否在父元素可视区域内（仅判断了垂直滚动）
 * @param elementNode 当前元素
 * @param containerNode 带滚动条的包裹父元素
 */
function isElementViewableInContainer(elementNode: HTMLDivElement, containerNode: HTMLDivElement) {
  // 子元素相对父元素顶部的距离
  const elementTop = elementNode.offsetTop;
  // 子元素底部相对父元素顶部的距离，使用offsetHeight将边框以内都算入子元素高度
  const elementBottom = elementNode.offsetTop + elementNode.offsetHeight;
  // 父元素滚动距离
  const containerTop = containerNode.scrollTop;
  // 父元素当前可视底部距离，使用clientHeight，不包含父元素滚动条及边框高度
  const containerBottom = containerNode.scrollTop + container.clientHeight;
  // 若子元素顶在父元素滚动后顶部之下 且 子元素底部在父元素滚动后底部之上，则判定为在滚动父元素的可视区域内
  if (elementTop >= containerTop && elementBottom <= containerBottom) return true;
  // 否则在可视区域外
  else return false;
}
```

### 方法二 使用 getBoundingClientRect

```ts
/**
 * 通过元素及其可滚动的父元素，判断当前元素是否在父元素可视区域内（仅判断了垂直滚动）
 * @param elementNode 当前元素
 * @param containerNode 带滚动条的包裹父元素
 */
function isElementViewableInContainer(elementNode: HTMLDivElement, containerNode: HTMLDivElement) {
  // 子元素rect
  const elementRect = elementNode.getBoundingClientRect();
  // 子元素顶部相对视窗口顶部的距离
  const elementTop = elementRect.top;
  // 子元素底部相对视窗口顶部的距离
  const elementBottom = elementRect.bottom;
  // 父元素rect
  const containerRect = containerNode.getBoundingClientRect();
  // 父元素顶部相对视窗口顶部的距离
  const containerTop = containerRect.top;
  // 父元素底部相对视窗口顶部的距离
  const containerBottom = containerRect.bottom;
  // 若子元素顶在父元素滚动后顶部之下 且 子元素底部在父元素滚动后底部之上，则判定为在滚动父元素的可视区域内
  if (elementTop >= containerTop && elementBottom <= containerBottom) return true;
  // 否则在可视区域外
  else return false;
}
```

### 方法三 使用 Intersection Observer

```ts
// 根元素
const root = document.getElementById("container");
// 目标元素
const target = document.getElementById("element");
// observer配置
const options = {
  root: root,
  rootMargin: "0px",
  threshold: 1,
};
// 是否可见
let isViewable = false;
// 回调函数
const callback = (entries) => {
  entries.forEach((entry) => {
    if (entry.target.id === root.id) {
      // 完全可见时相交比率为1
      isViewable = entry.intersectionRatio === 1;
    }
  });
};
// 新建观察者
const observer = new IntersectionObserver(callback, options);
// 监听目标元素
observer.observe(element);
```

> 参考资料
>
> [面试官：如何判断一个元素是否在可视区域中？](https://github.com/febobo/web-interview/issues/84)
>
> [如何判断元素是否在可视区域 ViewPort](https://segmentfault.com/a/1190000017150136)
>
> [What is offsetHeight, clientHeight, scrollHeight?](https://stackoverflow.com/questions/22675126/what-is-offsetheight-clientheight-scrollheight)
>
> [How to check if element is visible after scrolling?](https://stackoverflow.com/questions/487073/how-to-check-if-element-is-visible-after-scrolling)
>
> [MDN Intersection Observer API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)
