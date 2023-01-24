---
title: css保持盒模型纵横比
date: 2022-11-20 22:17:44
tags:
  - css
  - 纵横比
  - aspect-ratio
categories: css
---

在使用媒体查询与响应式布局过程中，我们常常需要让一个盒模型按一定的纵横比响应，即盒子的高度取决于盒子的宽度。常见需求如在响应过程中保持盒子为正方形，或保持盒子为 16:9 的比例。那么如何实现这样的需求呢？

## 可替换元素

如果是`<video>`或`<img>`等可替换元素标签，通常需要保持图片或视频素材本身纵横比，因此只需要固定`width`或`height`属性中的一个，并将另一个设置为`auto`即可按内容实现固定的纵横比。

## aspect-ratio

2022 年了，`aspect-ratio`属性已经成为了 css 标准，在主流的浏览器中都有不错的支持。使用纵横比属性，很容易就能实现盒模型的固定比例。

<!-- more -->

```css
.div1 {
  width: 50%;
  /* 默认值 auto，遵循浏览器原本宽高计算规则 */
  aspect-ratio: auto;
}
.div2 {
  width: 50%;
  /* 比值写法，1比1则宽高相等 */
  aspect-ratio: 1 / 1;
}
.div3 {
  width: 50%;
  /* 比值写法，分母为宽，分子为高 */
  aspect-ratio: 16 / 9;
}
.div4 {
  width: 50%;
  /* 小数写法，即宽是高的几倍 */
  aspect-ratio: 0.5;
}
```

尽管现代浏览器都已经支持了纵横比的属性，但是手机端微信浏览器，或其他国产浏览器，对纵横比属性的支持并不好。那么此时又该如何实现盒子的纵横比呢？

## margin/padding

相信熟悉 css 标准的小伙伴已经想到了办法，那就是利用`margin`和`padding`的百分比计算机制。因为`margin`和`padding`的百分比是相对宽`width`计算的。

原理：

1. 用`padding-top`或`padding-bottom`百分比来表示盒子高度是宽度的几倍;

2. 盒子高度`height`设置为 0，因为`padding`已经把盒子撑开到需要的高度了，盒子本身高度应为 0;

3. 新增一个子盒子作为实际内容盒子，因为父盒子高度为 0，无法承载内容;

4. 将子盒子相对父盒子绝对定位，并撑开至与父盒子大小一致，即在父盒子上增加`position: relative;`属性，在子盒子上增加`position: absolute; top: 0; right: 0; bottom: 0; left: 0;`属性;

```css
.parent {
  width: 50%;
  position: relative;
  padding-bottom: 100%;
}
.child {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
```

> 参考资料
>
> [Height equal to dynamic width (CSS fluid layout) ](https://stackoverflow.com/questions/5445491/height-equal-to-dynamic-width-css-fluid-layout)
>
> [aspect-ratio](https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio)
>
> [面试官：CSS 如何实现固定宽高比？](https://cloud.tencent.com/developer/article/1609830)
