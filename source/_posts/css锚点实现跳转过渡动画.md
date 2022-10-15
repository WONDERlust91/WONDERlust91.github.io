---
title: css锚点实现跳转过渡动画
date: 2022-02-09 17:03:00
tags:
  - a 标签
  - 锚点
  - anchor
  - 动画
categories: css
---

## 描点跳转

描点跳转的实现非常简单，a 标签 href 属性绑定要跳转的元素 id 即可。

```html
<!-- 跳转按钮 -->
<a href="#foo"></a>

<!-- 被跳转元素应该在一个被溢出的元素中 -->
<div class="overflowed-wrapper">
  <!-- 要跳转到的元素 -->
  <div id="foo"></div>
</div>
```

## 过渡动画

过去锚点跳转的过渡动画，常常使用 js 来实现，如今随着浏览器对 css `scroll-behavior` 属性的全面支持，可以使用该属性来实现平滑过渡动画。

```css
.overflowed-wrapper {
  scroll-behavior: smooth;
}
```

> 参考资料 [锚点跳转过渡动画](https://www.codeleading.com/article/52573150204/)
