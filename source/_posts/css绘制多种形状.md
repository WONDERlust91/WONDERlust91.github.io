---
title: css绘制多种形状
date: 2019-06-12 11:03:34
tags:
- 圆形
- 椭圆形
- 平行四边形
- 梯形
- 多边形
categories: css
---

CSS上古时代连圆形都要靠切图，到了CSS3之后，我们已经可以使用各种方法绘制不同的形状了

## 圆形

通常我们都是依靠设置宽高相同的盒，再设置border-radius为50%来绘制圆形

```css
.circle {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}
```

<!-- more -->

现在，我们也可以用强大的clip-path的circle方法来绘制，circle( [<shape-radius\>]? [at <position\>] )，其中shape-radius是半径，当使用百分比半径的时候将会以sqrt(width^2+height^2)/sqrt(2)为100%来计算，position定义了圆心到盒上边与左边的距离，默认值为盒中心。

```css
.circle {
  width: 100px;
  height: 100px;
  /* 所有参数示例 */
  clip-path: circle(40px at 50px 40px);
  /* 实现同border-radius一致效果的示例 */
  clip-path: circle();
}
```

## 椭圆形

clip-path绘制椭圆形，ellipse( [<shape-radius\>{2}]? [at <position\>] )，shape-radius为椭圆形x轴与y轴半径，默认为100%，position是椭圆中心点，默认为盒中心点。

```css
.ellipse {
  width: 100px;
  height: 50px;
  clip-path: ellipse(50% 30% at 50% 35%);
}
```

## 三角形

使用border属性，我们可以绘制出三角形，与梯形，这种方法的缺点是只绘出了形状，并没有改变盒模型的边界

```css
.triangle {
  width: 100px;
  height: 100px;
  /* 未设置顶部边框则宽为0，左右两侧边框宽度为div宽度的一半，透明色，底边框高度为div高度，使用背景色，三个边框形成的区域就是三角形 */
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 100px solid orange;
}
.trapezoid {
  width: 100%;
  height: 100px;
  /* 减小两侧边框宽度，使之形成梯形 */
  border-left: 30px solid transparent;
  border-right: 30px solid transparent;
  border-bottom: 100px solid orange;
}
```

## 平行四边形

绘制平行四边形，我们第一个想到的方法就是使用transform的skew，但是skew有一个很大的缺点就是连盒内部的文字也变形了，如何解决文字变形的问题呢？我们可以使用CSS的伪元素，在伪元素上使用skew变形，而不改变元素本身。

skew( ax [, ay] )，ax与ay是角的大小，ax指x轴（纵轴，不同与平面直接直角坐标系的x轴）在逆时针方向上扭曲的程度，ay指y轴在顺时针方向上扭曲的程度。角的大小可以用度deg，百分度grad，弧度rad，圈数turn来表示。

```css
/* 设置z-index为1，防止伪元素遮挡 */
.parallelogram {
  width: 150px;
  height: 36px;
  position: relative;
  cursor: pointer;
  font-size: 20px;
  color: #fff;
  line-height: 36px;
  z-index: 1;
}
.parallelogram::before {
  /* 设置z-index为-1，不遮挡未设置z-index的元素 */
  content: "";
  width: 150px;
  height: 36px;
  position: absolute;
  top: 0;
  left: 0;
  background: #00699f;
  transform: skew(-45deg);
  z-index: -1;
}
```

## 梯形

梯形用到了透视与3D变换的x轴旋转，若是要得到一个直角梯形，则还要改变变换原点的属性。

### 一些变换的基础知识

scale( sx [, sy] ) 缩放函数，sx表示缩放向量横坐标，sy表示缩放向量纵坐标，若未设置sy，则默认值为sx，使得元素保持原有形状均等缩放。

perspective(length) 观察者与z=0平面的距离，用以产生透视效果，默认值为none。

rotateX(a) 绕x轴顺时针旋转大小，可以是角度、弧度、百分度、圈数。

transform-origin 变换原点，可以是一个值、两个值或三个值，第一个值代表水平偏移量，第二个值表示垂直偏移量

* 一个值：
  * 必须是长度、百分比或left,center,right,top,bottom关键字中的一个。
* 两个值：
  * 其中一个必须是长度，百分比或left,center,right关键字中的一个。
  * 另一个必须是长度，百分比或top,center,bottom关键字中的一个。
* 三个值：
  * 前两个值同只有两个值的用法。
  * 第三个值必须是长度。它始终代表Z轴偏移量。

### 梯形与直角梯形

```css
.trapezoid {
  width: 170px;
  height: 36px;
  position: relative;
  cursor: pointer;
  font-size: 20px;
  color: #fff;
  line-height: 36px;
  z-index: 1;
}
.trapezoid::before {
  content: "";
  width: 170px;
  height: 36px;
  position: absolute;
  top: 0;
  left: 0;
  background: #00699f;
  /* 通过沿x轴旋转并改变观察点与z平面距离形成梯形，通过缩放使变换后的梯形达到原来的高度 */
  transform: scale(1.1 1.79) perspective(100px) rotateX(45deg);
  /* 通过改变变换原点为右下方，使梯形成为直角梯形 */
  transform-origin: bottom right;
  z-index: -1;
}
```

## 多边形

使用clip-path属性的polygon方法，可以更容易地画出三角形，四边形等，任意多边形形状。

polygon( [<fill-rule\>,]? [<shape-arg\> <shage-arg\>]# )

fill-rule决定了多边形内部的填充方式，可选值为nonzero和evenodd，默认为nonzero，后面的每对参数表示多边形以盒模型左上角顶点为原点的顶点坐标 x y。

```css
.polygon {
  width: 100px;
  height: 100px;
  /* 三角形 */
  clip-path: polygon(nonzero, 50% 0, 0 100%, 100% 100%);
  /* 平行四边形 */
  clip-path: polygon(nonzero, 50% 0, 0 100%, 50% 100%, 100% 0);
  /* 梯形 */
  clip-path: polygon(nonzero, 20% 0, 0 100%, 100% 100%, 80% 0);
}
```

clip-path属性虽然很方便，但是兼容性不好，大家可以根据不同的需求选择不同的方法。

参考连接
[MDN basic shape](https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape)
[MDN perspective](https://developer.mozilla.org/en-US/docs/Web/CSS/perspective)
[MDN scale()](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/scale)
[MDN rotateX()](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/rotateX)
[MDN transform-origin](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-origin)
[张鑫旭-CSS实现平行四边形布局效果](https://www.zhangxinxu.com/wordpress/2019/04/css-parallelogram-layout)
[使用CSS写出三角形、圆形、平行四边形、梯形](https://blog.csdn.net/kaelyn_X/article/details/78238041)
[dabblet](https://dabblet.com/gist/1345dc9399dc8e794502)
[不可思议的CSS之clip-path](https://segmentfault.com/a/1190000010936207)
[张鑫旭-好吧，CSS3 3D transform变换，不过如此！](https://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition)
