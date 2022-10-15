---
title: css让图片适应外层div大小
date: 2019-03-24 23:08:33
tags:
- css
- 图片
categories: css
---

# 让图片适应外层div大小

在我们展示网络资源图片时通常会遇到在一个固定的宽度内展示不同大小的图片，如果我们让图片撑满100%去适应外层div的大小，那么大于div尺寸的图片将按外层div大小展示，而小于外层div尺寸的图片则会被拉伸至外层div的尺寸，两个尺寸相差悬殊的话，小图会被拉伸地很模糊。

我们通过限定内层图片的最大宽度为外层div的宽度来使大于外层div尺寸的图片适配，而我们不去限定内层图片的宽度以使小于外层div尺寸的图片不被拉伸，直接展示。

```html
<div class="img-container">
  <img src="www.test.com/test.png" />
</div>

<style>
.img-container {
  /* 图片不要限定高，否则图片无法按照原有比例展示，会变形 */
  width: 650px; /* 若是外层还有div且宽度固定，则可以使用100% */
}
.img-container img {
  /* 图片不要限定高，否则图片无法按照原有比例展示，会变形 */
  max-width: 650px; /* 也可以使用100% */
}
</style>
```