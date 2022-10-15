---
title: javascript之Canvas自适应
date: 2019-06-14 10:42:30
tags:
- Canvas
- resize
categories: javascript
---

## Canvas自适应的问题

在使用Canvas的过程中，我们遇到的第一个问题就是屏幕自适应的问题，与普通的html标签不同，Canvas标签虽然支持长宽百分比的写法，但是内部绘制的图像并不适应百分比的缩放，我们会发现图像是糊的根本无法查看。好在浏览器的api中有一个resize事件，那么我们就利用resize事件来重新改变Canvas标签的长宽，来达到自适应的效果。

```html
<!-- 这里要注意的一点是使用了flex，因为没有flex属性时，canvas超出了container，产生了滚动条 -->
<div id="container" style="width: 100%; heigth: 100%; display: flex;">
  <canvas id="canvas"></canvas>
</div>
```

<!-- more -->

```javascript
const container = document.getElementById("container");
const canvas = document.getElementById("canvas");
// 设置canvas宽、高
canvas.width = container.clientWidth;
canvas.height = container.clientHeight;
const ctx = canvas.getContext("2d");
// 绘制函数
const draw = () => {
  ctx.clearRect(0, 0, canvas.clientWidth, canvas.clientHeight);
  ctx.fillStyle = "pink";
  ctx.fillRect(0, 0, canvas.clientWidth, canvas.clientHeight);
};
draw();
// 窗口大小改变时执行的函数
const onResize = () => {
  canvas.width = container.clientWidth;
  canvas.height = container.clientHeight;
  draw();
};
// 为窗口大小改变事件增加监听器
window.addEventListener("resize", onResize, false);
```

## three.js中的canvas自适应

在three.js中canvas的自适应又有所不同。我们先按照前面的思路来实现three.js下的自适应。前置环境：npm安装three.js。

```html
<div id="three"></div>
```

```javascript
import * as THREE from "three";
const threeContainer = document.getElementById("three");
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, threeContainer.clientWidth / threeContainer.clientHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();

renderer.setSize(threeContainer.clientWidth, threeContainer.clientHeight);
threeContainer.appendChild(renderer.domElement);

const onResize = () => {
  // 相机参数：宽高比
  camera.aspect = threeContainer.clientWidth / threeContainer.clientHeight;
  // 更新投影矩阵
  camera.updateProjectionMatrix();
  // 设置canvas宽高
  renderer.setSize(threeContainer.clientWidth, threeContainer.clientHeight);
};
window.addEventListener("resize", onResize, false);
```

使用之前canvas同样思路的方法，我们发现确实可以做到自适应了，但是窗口放大时没有问题，窗口缩小的时候右侧与底边会出现两条空白，这是为什么呢？

原来窗口缩小时，canvas区域大于窗口变化后的threeContainer区域，threeContainer区域出现了滚动条，这时读取到的threeContainer区域的clientWidth与clientHeight是不包含滚动条大小的。用此时读取到的threeContainer区域大小去设置three.js的canvas绘制区域，使canvas区域变小，threeContainer区域滚动条消失，最后就出现了两条空白。

解决方法非常简单，在threeContainer上增加一个css样式overflow:hidden即可，这样就不会有滚动条影响canvas区域的大小了。
