---
title: css子元素设置width或height为百分之百再设置margin或padding溢出
date: 2018-11-18 18:19:01
tags:
  - css
  - width 100% 溢出
categories: css
---

## 子元素 width: 100%后再设置 margin 或 padding 就会溢出父元素

```html
<div
  style="
    width: 200px;
    height: 50px;
    background: green;
    "
>
  <div
    style="
        width: 100%;
        height: 100%;
        background: pink;
        margin: 10px;
        "
  ></div>
</div>
```

如何解决？

### 一、父元素设置 padding，取消子元素上的 margin 与 padding 设置

```html
<div
  style="
    width: 200px;
    height: 50px;
    background: green;
    padding: 10px;
    "
>
  <div
    style="
        width: 100%;
        height: 100%;
        background: pink;
        "
  ></div>
</div>
```

### 二、box-sizing: border-box

若子元素只有 padding 设置而没有 margin 设置，则可以在子元素上设置 box-sizing: border-box; 这样子元素的宽度 100%将包含内容宽度、padding 和边框宽度三者。

```html
<div
  style="
    width: 200px;
    height: 50px;
    background: green;
    padding: 10px;
    "
>
  <div
    style="
        width: 100%;
        height: 100%;
        background: pink;
        padding: 10px;
        box-sizing: border-box;
        "
  ></div>
</div>
```

### 知识扩展：box-sizing 的两种取值

默认取值：content-box，元素占用宽度/高度 = width/height + padding + border + margin

border-box，元素占用宽度/高度 = width/height + margin，padding 与 border 包含在 width/height 中
