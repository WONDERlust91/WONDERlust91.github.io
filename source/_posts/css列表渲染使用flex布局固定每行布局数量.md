---
title: css列表渲染使用flex布局固定每行布局数量
date: 2019-02-11 16:43:06
tags:
- css
- flex
- 列表渲染
categories: css
---

# css列表渲染使用flex布局固定每行布局数量

在实际应用中，我们常常会使用for循环渲染输出html内容块，然后使用flex布局及flex布局中的flex-wrap属性来使循环渲染的内容块自动换行。这样做的弊端是当浏览器的宽度改变时每行的内容块数量也会改变，无法获得统一的视觉体验。如何使浏览器宽度改变时flex布局每行布局数量不变呢？

这里有一个小技巧，即对html内容块使用百分比宽度布局。

仅使用百分比宽度布局则会发现无法给内容块之间设置间距，每个内容块都是紧密相连的。大家可能会想到使用justify-content属性与align-items属性来增加间距，justify-content中的space-around、space-evenly等属性值可以使内容块水平分散对齐，但是最后一行如果未填满，也会被居中，视觉体验并不好。那么如何解决间距问题呢？

这里我们把刚刚的内容块作为一个容器，给内容块设置一个padding值。这样就有了间距。真正的内容放置在内容块内层的div中即可。

```html
<div class="container">
    <div class="block">
        <div class="content"></div>
    </div>
    <div class="block">
        <div class="content"></div>
    </div>
    <div class="block">
        <div class="content"></div>
    </div>
    <div class="block">
        <div class="content"></div>
    </div>
    <div class="block">
        <div class="content"></div>
    </div>
</div>
```

```css
.container {
    display: flex;
    flex-wrap: wrap;
    background: red;
    width: 100%;
    height: 100%;
}
.block {
    width: 25%; /* 每行4个 */
    width: 20%; /* 每行5个 */
    background: green;
    padding: 5px;
}
.content {
    width: 100%;
    height: 100%;
    background: blue;
}
```