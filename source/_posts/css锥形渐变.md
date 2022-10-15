---
title: css锥形渐变
date: 2021-10-12 19:14:31
tags:
  - conic-gradient
  - 锥形渐变
categories: css
---

## 锥形渐变简介

锥形渐变函数会创建一个围绕中心点旋转的颜色渐变图像，由于创建出的图像有锥形的效果，故称为锥形渐变。锥形渐变函数的返回值是渐变数据类型的对象，与其他渐变一样，可以理解为是一张图片，故只能在 background 属性上使用，而不能用于 background-color 属性。

锥形渐变颜色随旋转角度改变而改变，与半径无关，同一角度上不论半径为多少，都是相同的颜色。可以看出这与我们熟知的径向渐变不同，径向渐变的颜色随半径长短改变而改变，与角度无关，同一半径上无论角度为多少，都是相同的颜色。

使用锥形渐变我们可以轻易地实现出饼图、调色盘、棋盘等效果。

<!-- more -->

## 锥形渐变语法

`conic-gradient( [from <angle> ]? [ at <position> ]?, <angular-color-stop | color-hint>,)`

- `<angle>` 定义了顺时针渐变的起始角度，可缺省，默认为 0 度；该属性需要使用角度单位值，如 deg、rad、turn 等，角度值前面必需要使用 from 关键字；

- `<position>` 定义了渐变的中心位置，可缺省，默认为 center；该属性使用与 background-position 属性相同的值，位置值前端必需使用 at 关键字；

- `<angular-color-stop>` 定义了渐变的断点颜色与位置，后面可以跟一个或两个断点位置，也可省略断点位置，断点位置为角度或百分比值；

  - 若断点位置省略，则默认在圆周上平均分配断点颜色，后在断点间线性插值渐变；

  - 当断点位置为一个值时，则在值对应的角度或百分比位置上放置颜色断点，后在各断点间线性插值渐变；

  - 若断点位置为两个值，则对应角度或百分比区间均为该色的纯色；

- `<color-hint>` 定义了插值中点的位置，即为单个的断点位置且没有断点颜色，要求相邻两个值必需为有颜色的`<angular-color-stop>`，在没有定义插值中点的时候，默认相邻两个色的中间色放置在 50%的中间角度上，定义了插值中点位置后，中间色放置在定义的插值中点位置上；

- 一个 断点颜色与位置 或 一个插值中点位置 为一组，可以有多组，每组间用逗号分隔；

## 使用示例

### 示例 1

基本的锥形渐变示例，下面代码中的示例均实现了相同的锥形渐变效果

```css
background: conic-gradient(#f06, gold);
background: conic-gradient(at 50% 50%, #f06, gold);
background: conic-gradient(from 0deg, #f06, gold);
background: conic-gradient(from 0deg at center, #f06, gold);
background: conic-gradient(#f06 0%, gold 100%);
background: conic-gradient(#f06 0deg, gold 1turn);
```

<div style="width: 300px; height: 200px; background: conic-gradient(#f06, gold);"></div>

### 示例 2

下面是断点位置在圆周一周范围之外的示例，示例均实现了相同的效果，在圆周之外的颜色断点不会显示，但依然会影响渐变。

例如在 0deg 位置与 100deg 位置不是纯白色与纯黑色，而是渐变过程中的颜色。

```css
background: conic-gradient(white -50%, black 150%);
background: conic-gradient(white -180deg, black 540deg);
background: conic-gradient(hsl(0, 0%, 75%), hsl(0, 0%, 25%));
```

<div style="width: 300px; height: 200px; background: conic-gradient(white -50%, black 150%);"></div>

### 示例 3

下面示例是旋转了起始角度的渐变，两个示例效果相同，一个通过改变起始角度实现，一个通过色相饱和度实现。

```css
background: conic-gradient(from 45deg, white, black, white);
background: conic-gradient(hsl(0, 0%, 75%), white 45deg, black 225deg, hsl(0, 0%, 75%));
```

<div style="width: 300px; height: 200px; background: conic-gradient(from 45deg, white, black, white);"></div>

需要注意的是，改变断点的位置并不完全按照预想方式生效，并可能会产生完全不同的渐变。

例如下面改变了所有颜色的断点位置，想实现同之前两个示例一样的旋转效果，并未生效。因为渐变默认是从 0deg 开始，设置第一个断点位置为 45deg 并不会从 45deg 开始，而是将第一个颜色设置为 0deg-45deg 的范围中，相当于 0deg 45deg。

```css
background: conic-gradient(white 45deg, black 225deg, white 405deg);
```

<div style="width: 300px; height: 200px; background: conic-gradient(white 45deg, black 225deg, white 405deg);"></div>

### 示例 4

将圆锥渐变与径向渐变组合，可以实现出调色盘的效果，也可以用色相饱和度实现。

```css
background: radial-gradient(gray, transparent), conic-gradient(red, magenta, blue, aqua, lime, yellow, red);
background: conic-gradient(
  hsl(360, 100%, 50%),
  hsl(315, 100%, 50%),
  hsl(270, 100%, 50%),
  hsl(225, 100%, 50%),
  hsl(180, 100%, 50%),
  hsl(135, 100%, 50%),
  hsl(90, 100%, 50%),
  hsl(45, 100%, 50%),
  hsl(0, 100%, 50%)
);
```

<div style="width: 200px; height: 200px; background: radial-gradient(gray, transparent), conic-gradient(red, magenta, blue, aqua, lime, yellow, red); border-radius: 50%;"></div>

### 示例 5

圆锥渐变还可以用来绘制饼图。因为断点位置中的 0deg 将会自动修正为上个断点的结束位置，这将在两个颜色断点间产生无限小的渐变（这个渐变并不可见），这就可以更方便地绘出纯色区域。

```css
background: conic-gradient(yellowgreen 0% 40%, gold 40% 75%, #f06 75% 100%);
/* 使用下面这种方式，效果一样，但更方便 */
background: conic-gradient(yellowgreen 40%, gold 0deg 75%, #f06 0deg);
```

<div style="width: 200px; height: 200px; background: conic-gradient(yellowgreen 40%, gold 0deg 75%, #f06 0deg); border-radius: 50%;"></div>

### 示例 6

与线性渐变和径向渐变相同，锥形渐变也有重复渐变 repeating-conic-gradient 方法。

```css
background: repeating-conic-gradient(gold, #f06 20deg);
```

<div style="width: 300px; height: 200px; background: repeating-conic-gradient(gold, #f06 20deg);"></div>

下面是一个纯色的重复锥形渐变示例

```css
background: repeating-conic-gradient(hsla(0, 0%, 100%, 0.2) 0deg 15deg, hsla(0, 0%, 100%, 0) 0deg 30deg) #0ac;
```

<div style="width: 300px; height: 200px; background: repeating-conic-gradient(hsla(0,0%,100%,.2) 0deg 15deg,hsla(0,0%,100%,0) 0deg 30deg) #0ac;"></div>

### 示例 7

使用锥形渐变还可以轻松实现棋盘。

```css
background: conic-gradient(black 25%, white 0deg 50%, black 0deg 75%, white 0deg);
background-size: 60px 60px;
/* 使用repeat-conic-gradient代码更简单 */
background: repeating-conic-gradient(black 0deg 25%, white 0deg 50%);
background-size: 60px 60px;
```

<div style="width: 300px; height: 200px; background: conic-gradient(black 25%, white 0deg 50%, black 0deg 75%, white 0deg); background-size: 60px 60px;"></div>

> 参考资料
>
> [CSS Images Module Level 4](https://drafts.csswg.org/css-images-4/Overview.bs#conic-gradients)
>
> [MDN conic-gradient](<https://developer.mozilla.org/en-US/docs/Web/CSS/gradient/conic-gradient()>)
>
> [CSS conic-gradient()锥形渐变简介](https://www.zhangxinxu.com/wordpress/2020/04/css-conic-gradient/)
>
> [神奇的 conic-gradient 圆锥渐变](https://www.cnblogs.com/coco1s/p/7079529.html)
