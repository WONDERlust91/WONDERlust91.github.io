---
title: css扇形画法
date: 2021-07-15 09:46:24
tags:
  - 扇形
  - css
categories: css
---

有很多扇形的实现方式，大部分是通过边框、遮罩等方式实现的，需要用到多个 div，这涉及到多个层级，且因为是遮罩，无法实现半透明等效果。下面讲一种使用`clip-path`属性实现扇形的方法。

## 基本原理

<!-- more -->

- 绘制一个正方形 div;<div style="width: 50px; height: 50px; background-color: #999; margin: 6px 0;"></div>
- 使用 `clip-path` 属性的 `polygon` 方法裁剪要显示的区域;
  - `polygon`方法以 div 中心 **50% 50%** 为起点，左上角原点 **0% 0%** 为不动点，将第三点从左上角原点开始沿四条边移动（每经过一个顶点，就增加该顶点为固定点），绘制要裁剪的区域;
  - div 顶边代表 0-90 度角的扇形（比率中的 0-25%），此时只需要中心起点、左上角原点、沿顶边动态变化的点，三个点，即可绘出要裁剪的三角形;<div style="width: 50px; height: 50px; background-color: #999; margin: 6px 0;"><div style="width: 50px; height: 50px; background-color: red; clip-path: polygon(50% 50%, 0% 0%, 60% 0%)"></div></div>
  - div 右边代表 90-180 度角的扇形（比率中的 25%-50%），此时需要中心起点、左上角原点、右上角顶点、沿右边动态变化的点，四个点，即可绘出要裁剪的多边形;<div style="width: 50px; height: 50px; background-color: #999; margin: 6px 0;"><div style="width: 50px; height: 50px; background-color: red; clip-path: polygon(50% 50%, 0% 0%, 100% 0%, 100% 60%)"></div></div>
  - div 下边代表 180-270 度角的扇形（比率中的 50%-75%），此时需要中心起点、左上角原点、右上角顶点、右下角顶点、沿下边动态变化的点，五个点，即可绘出要裁剪的多边形;<div style="width: 50px; height: 50px; background-color: #999; margin: 6px 0;"><div style="width: 50px; height: 50px; background-color: red; clip-path: polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, 40% 100%)"></div></div>
  - div 左边代表 270-360 度角的扇形（比率中的 75%-100%），此时需要中心起点、左上角原点、右上角顶点、右下角顶点、左下角顶点、沿左边动态变化的点，六个点，即可绘出要裁剪的多边形;<div style="width: 50px; height: 50px; background-color: #999; margin: 6px 0;"><div style="width: 50px; height: 50px; background-color: red; clip-path: polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, 0% 100%, 0% 40%)"></div></div>
- 使用 `border-radius` 属性将正方形 div 变为圆形，即可由多边形裁剪出扇形;<div style="width: 50px; height: 50px; background-color: #999; border-radius: 50%; margin: 6px 0;"><div style="width: 50px; height: 50px; background-color: red; clip-path: polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, 0% 100%, 0% 40%); border-radius: 50%;"></div></div>
- 将该 div 顺时针旋转 45 度，即可得到以正上方为 0 度的扇形;<div style="width: 50px; height: 50px; background-color: #999; border-radius: 50%; transform: rotate(45deg); margin: 6px 0;"><div style="width: 50px; height: 50px; background-color: red; clip-path: polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, 0% 100%, 0% 40%); border-radius: 50%;"></div></div>

## 需要注意的点

1. 0-180 度扇形使用的是 div 的顶边、与右边，动态点移动时都是从横坐标或纵坐标的 0%往 100%移动，而 180-360 度扇形则使用 div 的底边与左边，横、纵坐标的移动是从 100%往 0%变化;

2. 如果要通过 transition 或 animate 属性给 div 加上动效，使之从 0-360 度变化不突兀，那么 clip-path 的 polygon 方法顶点数应该相同，因为 transition 与 animate 需要 clip-path 的 polygon 顶点数相同，才能对动画进行补间;

## 示例与代码

<style>
.sector {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  transform: rotate(45deg);
  transition: clip-path 0.2s;
}
</style>
<div class="sector"></div>
<input type="range" name="sector" id="range" min="0" max="100">
<script>
const sectorDom = document.querySelector(".sector");
function setSector(dom, options) {
  dom.style.backgroundColor = options.color;
  if (options.ratio >= 0 && options.ratio <= 25) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, ${(options.ratio / 25) * 100}% 0%, ${
      (options.ratio / 25) * 100
    }% 0%, ${(options.ratio / 25) * 100}% 0%, ${(options.ratio / 25) * 100}% 0%)`;
  } else if (options.ratio > 25 && options.ratio <= 50) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, 100% 0%, 100% ${((options.ratio - 25) / 25) * 100}%, 100% ${
      ((options.ratio - 25) / 25) * 100
    }%, 100% ${((options.ratio - 25) / 25) * 100}%)`;
  } else if (options.ratio > 50 && options.ratio <= 75) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, ${(1 - (options.ratio - 50) / 25) * 100}% 100%, ${
      (1 - (options.ratio - 50) / 25) * 100
    }% 100%)`;
  } else if (options.ratio > 75 && options.ratio <= 100) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, 0% 100%, 0% ${
      (1 - (options.ratio - 75) / 25) * 100
    }%)`;
  } else {
    console.error("请在options.ratio属性上输入0-100的数字表示比率范围");
  }
}
const inputRangeDom = document.getElementById("range");
if (sectorDom && inputRangeDom) {
  setSector(sectorDom, { color: "red", ratio: 50 });
  inputRangeDom.addEventListener("change", (event) => {
    setSector(sectorDom, { color: "red", ratio: event.target.value });
  });
}
</script>

html 部分

```html
<div class="sector"></div>
<input type="range" name="sector" id="range" min="0" max="100" />
```

css 部分

```css
.sector {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  transform: rotate(45deg);
  transition: clip-path 0.2s;
}
```

js 部分

```js
const sectorDom = document.querySelector(".sector");
/**
 * 设置扇形的方法
 * @param {HTMLDivElement} dom 用于制作扇形的DOM节点
 * @param {{color: string; ratio: number}} options 配置扇形颜色与百分比，0度为0，360度为100
 */
function setSector(dom, options) {
  // 设置颜色
  dom.style.backgroundColor = options.color;
  // 0-25%，即顶边，多边形只需要前三个点，后三个点为第三个点的复制，为正常显示补间动画
  if (options.ratio >= 0 && options.ratio <= 25) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, ${(options.ratio / 25) * 100}% 0%, ${
      (options.ratio / 25) * 100
    }% 0%, ${(options.ratio / 25) * 100}% 0%, ${(options.ratio / 25) * 100}% 0%)`;
    // 25%-50%，即右边，多边形需要前四个点，后两个点为第四个点的复制，为正常显示补间动画
  } else if (options.ratio > 25 && options.ratio <= 50) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, 100% 0%, 100% ${((options.ratio - 25) / 25) * 100}%, 100% ${
      ((options.ratio - 25) / 25) * 100
    }%, 100% ${((options.ratio - 25) / 25) * 100}%)`;
    // 50%-75%，即底边，底边时横坐标为100%到0%，注意坐标反向；多边形需要前五个点，后一个点为第五个点的复制，为正常显示补间动画
  } else if (options.ratio > 50 && options.ratio <= 75) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, ${(1 - (options.ratio - 50) / 25) * 100}% 100%, ${
      (1 - (options.ratio - 50) / 25) * 100
    }% 100%)`;
    // 75%-100%，即左边，左边时纵坐标为100%到0%，注意坐标反向；多边形需要六个点
  } else if (options.ratio > 75 && options.ratio <= 100) {
    dom.style.clipPath = `polygon(50% 50%, 0% 0%, 100% 0%, 100% 100%, 0% 100%, 0% ${
      (1 - (options.ratio - 75) / 25) * 100
    }%)`;
  } else {
    console.error("请在options.ratio属性上输入0-100的数字表示比率范围");
  }
}
const inputRangeDom = document.getElementById("range");
if (sectorDom && inputRangeDom) {
  setSector(sectorDom, { color: "red", ratio: 50 });
  inputRangeDom.addEventListener("change", (event) => {
    setSector(sectorDom, { color: "red", ratio: event.target.value });
  });
}
```

> 参考资料
>
> [CSS3 教程：真·任意角度扇形画法](https://vlambda.com/wz_Y7azcoZwx.html)
>
> [第 76 天 用 CSS 画出一个任意角度的扇形，可以写多种实现的方法](https://github.com/haizlin/fe-interview/issues/527)
>
> [pie chart - clip-path](https://codepen.io/liuxiaole-the-sasster/pen/Zdrmxg)
