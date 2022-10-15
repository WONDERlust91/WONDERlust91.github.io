---
title: css实现四角边框
date: 2018-11-09 10:25:30
tags: 
- css
- 四角边框
categories: css
---

# css实现四角边框

![border](/images/border.svg)

如何实现上图这种四角的边框？

```html
<div class="main">
    <span>
    <span>
    <span>
    <span>
</div>
```

```css
.main {
    position: relative;
}
.main span:nth-child(1) {
    /* 绝对定位 */
    position: absolute;
    /* 将span撑开 */
    padding: 6px 6.5px;
    /* 边框宽度，左上角只显示左边框和上边框 */
    border-width: 3px 0 0 3px;
    border-style: solid;
    border-color: #4ab2f8;
    /* 定位到左上角，并使边框离main有一定间距 */
    left: -5px;
    top: -5px;
}
.main span:nth-child(2) {
    /* 绝对定位 */
    position: absolute;
    /* 将span撑开 */
    padding: 6px 6.5px;
    /* 边框宽度，右上角只显示右边框和上边框 */
    border-width: 3px 3px 0 0;
    border-style: solid;
    border-color: #4ab2f8;
    /* 定位到右上角，并使边框离main有一定间距 */
    right: -5px;
    top: -5px;
}
.main span:nth-child(3) {
    /* 绝对定位 */
    position: absolute;
    /* 将span撑开 */
    padding: 6px 6.5px;
    /* 边框宽度，左下角只显示左边框和下边框 */
    border-width: 0 0 3px 3px;
    border-style: solid;
    border-color: #4ab2f8;
    /* 定位到左下角，并使边框离main有一定间距 */
    left: -5px;
    bottom: -5px;
}
.main span:nth-child(4) {
    /* 绝对定位 */
    position: absolute;
    /* 将span撑开 */
    padding: 6px 6.5px;
    /* 边框宽度，右下角只显示右边框和下边框 */
    border-width: 0 3px 3px 0;
    border-style: solid;
    border-color: #4ab2f8;
    /* 定位到右下角，并使边框离main有一定间距 */
    right: -5px;
    bottom: -5px;
}
```

> 参考链接
> [https://www.jb51.net/css/581071.html](https://www.jb51.net/css/581071.html)