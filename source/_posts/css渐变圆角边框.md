---
title: css渐变圆角边框
date: 2022-02-13 21:40:00
tags:
  - mask
  - border-image
  - background-image
  - 渐变圆角边框
categories: css
---

## 需求功能

需要实现一个渐变圆角边框的 div，且不仅边框是渐变色，要求 div 内部也是另一种**带透明度的**渐变色。(注意，下面所有示例都基于 css 属性 box-sizing: border-box)

<div style="
  box-sizing: border-box;
  width: 200px;
  height: 100px;
  padding: 5px;
  border-radius: 6px;
  position: relative;
  background: linear-gradient(
    to right,
    rgba(128, 128, 128, 0.5),
    rgba(0, 0, 0, 0)
  );
">
  <div style="
    box-sizing: border-box;
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    padding: 5px;
    border-radius: 6px;
    background: linear-gradient(to right, #9c20aa, #fb3570) border-box;
    mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0) border-box;
    mask-composite: exclude;
    -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(
          #fff 0 0
        ) border-box;
    -webkit-mask-composite: destination-out;
  "></div>
</div>

## 实现方案

### 方案 1——双层 div 叠加

能想到最直接的方案就是两层 div 叠加，外层 div 较大，负责边框渐变色，内层 div 较小，负责内部区域颜色。但是由于是两层 div 叠加，内层要实现的渐变色如果没有透明度，是没问题的；若是内层 div 要实现的渐变色带有透明度，外层颜色就会透过透明的内层显示出来，这个方案就不适用了。

内层无透明度，正常显示

<div style="
  margin-bottom: 10px;
  box-sizing: border-box;
  width: 200px;
  height: 100px;
  background-image: linear-gradient(to right, #9c20aa, #fb3570);
  border-radius: 6px;
  position: relative;
">
  <div style="
    box-sizing: border-box;
    width: 190px;
    height: 90px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-image: linear-gradient(
      to right,
      rgba(128, 128, 128, 1),
      rgba(0, 0, 0, 1)
    );
    border-radius: 6px;
  "></div>
</div>

内层有透明度，底色透出来了

<div style="
  box-sizing: border-box;
  width: 200px;
  height: 100px;
  background-image: linear-gradient(to right, #9c20aa, #fb3570);
  border-radius: 6px;
  position: relative;
">
  <div style="
    box-sizing: border-box;
    width: 190px;
    height: 90px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-image: linear-gradient(
      to right,
      rgba(128, 128, 128, 0.5),
      rgba(0, 0, 0, 0)
    );
    border-radius: 6px;
  "></div>
</div>

<!-- more -->

```html
<div class="test1">
  <div class="test1-inner"></div>
</div>

<style>
  .test1 {
    width: 200px;
    height: 100px;
    background-image: linear-gradient(to right, #9c20aa, #fb3570);
    border-radius: 6px;
    position: relative;
  }
  .test1-inner {
    /* 内比外小，尺寸差为边框宽度的两倍 */
    width: 190px;
    height: 90px;
    /* 内层居中 */
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    /* 内层渐变色，透明度必需为1，否则外层色露出，故不适用内层有渐变色的渐变 */
    background-image: linear-gradient(
      to right,
      rgba(128, 128, 128, 1),
      rgba(0, 0, 0, 1)
    );
    /* 内层圆角 */
    border-radius: 6px;
  }
</style>
```

### 方案 2——边框图片

使用 css 边框图片属性 border-image 实现，不需要嵌套 div 实现，更直观，也支持带有透明度的背景色，但缺点是不支持圆角设置。

<div style="
  width: 200px;
  height: 100px;
  background: linear-gradient(
    to right,
    rgba(128, 128, 128, 0.5),
    rgba(0, 0, 0, 0)
  );
  border: 5px solid;
  border-image-source: linear-gradient(to right, #9c20aa, #fb3570);
  border-image-slice: 1;
  border-radius: 6px;
"></div>

```html
<div class="test2"></div>

<style>
  .test2 {
    width: 200px;
    height: 100px;
    background: linear-gradient(
      to right,
      rgba(128, 128, 128, 0.5),
      rgba(0, 0, 0, 0)
    );
    border: 5px solid;
    /* 背景图片 */
    border-image-source: linear-gradient(to right, #9c20aa, #fb3570);
    /* 背景图片九宫格切割参数，决定背景图片在边框上的展示范围 */
    /* 由于我们是渐变色，切割参数设置为非0值即可，因为切割区域大小，不影响渐变色的展示 */
    border-image-slice: 1;
    /* 圆角设置，仅对纯色边框生效，对边框图片属性无效 */
    border-radius: 6px;
  }
</style>
```

### 方案 3——双层背景叠加

与方案 1 原理一样，只不过不再使用两个 div，而是直接使用 css 背景图的多层叠加特性实现。缺点也同方案 1 一样，不支持内层有透明度。

需要特别注意的是，多层背景图片，后书写的在底层，先书写的在顶层。

内层无透明度，正常显示

<div style="
  margin-bottom: 10px;
  width: 200px;
  height: 100px;
  padding: 5px;
  background-image: linear-gradient(
      to right,
      rgba(128, 128, 128, 1),
      rgba(0, 0, 0, 1)
    ), linear-gradient(to right, #9c20aa, #fb3570);
  background-clip: content-box, border-box;
  border-radius: 6px;
"></div>

内层有透明度，底色透出

<div style="
  width: 200px;
  height: 100px;
  padding: 5px;
  background-image: linear-gradient(
      to right,
      rgba(128, 128, 128, 0.5),
      rgba(0, 0, 0, 0)
    ), linear-gradient(to right, #9c20aa, #fb3570);
  background-clip: content-box, border-box;
  border-radius: 6px;
"></div>

```html
<div class="test3"></div>

<style>
  .test3 {
    width: 200px;
    height: 100px;
    /* 通过 padding 留出边框距离 */
    padding: 5px;
    /* 两层背景图设置，注意两层间用逗号分隔，顶层依然无法支持带透明的颜色 */
    background-image: linear-gradient(
        to right,
        rgba(128, 128, 128, 1),
        rgba(0, 0, 0, 1)
      ), linear-gradient(to right, #9c20aa, #fb3570);
    /* 顶层占用 content-box，底层占用 border-box */
    background-clip: content-box, border-box;
    border-radius: 6px;
  }
</style>
```

### 方案 4——css mask

使用 css 的 mask 属性，可以说是最佳实现方案了，可以完美支持边框渐变、中间区域背景带有透明度、边框圆角等所有需求。不过由于还处于推荐候选草案（Candidate Recommendation Draft）阶段，兼容性稍差，IE 浏览器不支持，webkit 内核的浏览器需要增加`-webkit-`前缀，一般脚手架已通过 babel 做自动兼容，无需手动添加前缀。不过下面的示例中，还是都添加了。

实现原理：先建立一个 div，为 div 设置圆角和内边距（内边距即最终的边框宽度），并设置为相对位置`position: relative`（方便内部伪元素相对该元素定位）；再建立一个伪元素使用 mask 来实现圆角渐变的边框，伪元素与外层 div 圆角值及内边距一致即可，伪元素使用绝对定位，这样父元素使用任何布局都不会影响到伪元素。

<div style="
  width: 200px;
  height: 100px;
  padding: 5px;
  border-radius: 6px;
  position: relative;
  background: linear-gradient(
    to right,
    rgba(128, 128, 128, 0.5),
    rgba(0, 0, 0, 0)
  );
">
  <div style="
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    padding: 5px;
    border-radius: 6px;
    background: linear-gradient(to right, #9c20aa, #fb3570) border-box;
    mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0) border-box;
    mask-composite: exclude;
    -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(
          #fff 0 0
        ) border-box;
    -webkit-mask-composite: destination-out;
  "></div>
</div>

```html
<div class="test4"></div>

<style>
  .test4 {
    width: 200px;
    height: 100px;
    padding: 5px;
    border-radius: 6px;
    position: relative;
    background: linear-gradient(
      to right,
      rgba(128, 128, 128, 0.5),
      rgba(0, 0, 0, 0)
    );
  }
  .test4::before {
    content: "";
    /* 绝对定位，四个方向值都设置为0，不设宽高，相当于宽高100% */
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    /* 内边距与圆角值与外部一样，内边距表示边框宽度 */
    padding: 5px;
    border-radius: 6px;
    /* 背景渐变色为边框色 */
    background: linear-gradient(to right, #9c20aa, #fb3570) border-box;
    /* 设置两层全透明遮罩，上层后绘制，故称为源层，在内边距以内，下层先绘制，故为目标层，在边框以内 */
    /* 注意 这里与多层背景先写的 */
    mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0) border-box;
    /* exclude 即排除多层 mask 重叠部分，exclude 仅 firefox 支持 */
    mask-composite: exclude;
    /* webkit 兼容写法 */
    -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(
          #fff 0 0
        ) border-box;
    /* webkit 支持属性与 firefox 不一致，更像是 Canvas 的 globalCompositeOperation 属性，只是不如 Canvas 中支持的属性多，且个别属性表现行为稍有不一致。*/
    /* destination-out 指 目标遮罩与源遮罩重叠部分全部排除，显示为透明，仅保留目标遮罩不重叠部分 */
    -webkit-mask-composite: destination-out;
  }
</style>
```

最佳方案中用到了 CSS Mask 属性，可以在另一篇文章——{% post_link css遮罩mask %}中详细了解 CSS Mask。

> 参考资料
>
> [Border Gradient with Border Radius](https://stackoverflow.com/questions/51496204/border-gradient-with-border-radius)
>
> [Possible to use border-radius together with a border-image which has a gradient?](https://stackoverflow.com/questions/5706963/possible-to-use-border-radius-together-with-a-border-image-which-has-a-gradient)
>
> [CSS3 的 border-image-slice 属性详细介绍](https://blog.csdn.net/weixin_43606158/article/details/104940881)
