---
title: javascript让div元素监听键盘事件
date: 2021-09-15 14:00:24
tags:
  - div
  - 键盘事件
categories: javascript
---

## 使用背景

通常我们在页面中使用一个截屏按钮，通过点击截屏按钮来截图。业务中发现截屏时如果想要展示一些鼠标 hover 才能展示的数据时，与点击按钮截屏相冲突。故需要使用键盘截屏。

根据业务需求，需要在某个特定的 div 区域绑定键盘事件，发现在 div 上绑定键盘事件没有效果。

## 键盘事件

keydown 事件触发于键盘按下的时候，keyup 事件触发于键盘抬起的时候。与 keypress 事件（keypress 事件已弃用）不同，所有按键都会触发 keyup、keydown 事件，而 keypress 事件只有产生字符值的按键才会触发。

重点：**键盘事件只能由`<inputs>`,`<textarea>`以及任何具有`contentEditable`或`tabindex=-1`属性的组件触发**

这也就解释了为什么在 div 元素上绑定键盘事件无效了。因为 div 元素不是 contentEditable 元素也没有 tabindex 属性。

## tabindex

tabindex 是一个所有的 html 元素都有的全局属性，该属性表示当前元素是否可以聚焦（focus），以及它以何种顺序参与键盘导航。

- tabindex 为负值（通常设置为-1），表示元素是可聚焦的，但是不能通过键盘导航访问到该元素。

- tabindex 为 0，表示元素是可聚焦的，并且可以通过键盘导航聚焦到该元素，它的相对顺序是根据元素在 DOM 中的顺序决定的。

- tabindex 为正值，表示元素可聚焦，且可以通过键盘导航聚焦，它的相对顺序根据 tabindex 递增而滞后获得焦点。若多个元素有相同 tabindex，则相对顺序按照它们在 DOM 中的顺序确定。

通常值为 0、非法值、或没有 tabindex 值的元素应该放到 tabindex 值为正值的元素后面。tabindex 最大不能超过 32767。

## 解决方案

给 div 元素设置`tabindex=-1`即可在 div 元素上监听键盘事件。

```tsx
// 在tsx中
<div tabIndex={-1} onKeyDown={(event) => console.log(event)}></div>
```

> 参考资料
>
> [div 元素监听键盘事件](https://www.dazhuanlan.com/starit/topics/1345812)
>
> [MDN-Element: 键盘按下事件](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/keydown_event)
>
> [MDN-tabindex](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/tabindex)
