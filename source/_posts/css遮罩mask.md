---
title: css遮罩mask
date: 2022-02-16 10:47:39
tags:
  - mask
  - 遮罩
categories: css
---

{% post_link css渐变圆角边框 %}一文中提到了 Mask，那么就来详细了解下 CSS Mask。

Mask 遮罩，就是原始图片只显示遮罩图片不透明部分。

## 兼容性

CSS Mask 属性还不是标准，暂时还处于推荐候选草案阶段，不过只要进入了推荐候选草案阶段，那么成为标准就只是时间问题了。可以放心使用。

Firefox 对 CSS Mask 全兼容，无需任何内核前缀，部分属性值与其他浏览器不一致需要注意。例如 mask-composite 取值与 Chrome 不一致。

IE 不支持 Mask 属性。

其他浏览器对 Mask 属性支持不完全，**其中 webkit 内核浏览器需要使用 -webkit- 前缀**。

详见[Can I use CSS Masks?](https://caniuse.com/?search=mask)

## 简介

起初，mask 属性使用时只有自己本身，随着规范化，mask 属性成为了诸多 mask-\* 属性的缩写，类似于 background、border 属性。

mask 属性列表：

- mask-image 遮罩图片
- mask-mode 遮罩图片的模式
- mask-position 遮罩图片的位置
- mask-size 遮罩的大小
- mask-repeat 遮罩图片重复性
- mask-clip 遮罩图片裁剪
- mask-origin 遮罩原点
- mask-type 遮罩类型
- mask-composite 遮罩组合方式

<!-- more -->

## mask-image

mask-image 指遮罩使用的图片资源，类似 background-image，可支持多个值，默认值为 none，无遮罩图片。该属性支持各种图片资源：

1. url() 功能符引入的静态图片资源，如 png、svg 等，很少用 jpg 图片遮罩，因为 jpg 完全不透明，最终效果和直接原图一致;
2. CSS3 各类渐变绘制的图片;
3. image() 、element() 等功能符，因兼容性不佳，不展开;

## mask-mode

遮罩模式，默认值为 match-source，即根据资源类型自动使用合适的遮罩模式。例如遮罩使用 SVG 中的`<mask>`元素，此时的 match-source 计算值为 luminance，基于亮度遮罩。如果是其他情况，则计算值是 alpha，基于透明度遮罩。

mask-mode 可选值：

- luminance 亮度遮罩;
- alpha 透明度遮罩;
- match-source 根据资源自动选择前两种类型;

该属性仅 Firefox 支持，其他浏览器不支持。

## mask-position

遮罩位置属性，与 background-position 支持的值和表现基本一致，默认值为`0% 0%`，相对左上角定位。Chrome 浏览器需要 `-webkit` 前缀。

mask-position 可选值：

- top、bottom、left、right、center 等关键字;
- 10%、10px、10rem 等各类数值;

双值表示两个轴向，多值对应表示多个遮罩图片。

## mask-size

遮罩图片尺寸，与 background-size 类似，默认为 auto，Chrome 浏览器需要 `-webkit` 前缀。

mask-size 可选值：

- auto、contain、cover 等关键字;
- 10px、10%、10em 等各类数值;

双值表示两个轴向，多值对应表示多个遮罩图片。

## mask-repeat

遮罩图片重复性，与 background-repeat 类似，默认值为 repeat，Chrome 浏览器需要 `-webkit` 前缀。

mask-repeat 可选值：

- repeat、repeat-x、repeat-y、space、round、no-repeat 等关键字;

双值表示两个轴向，多值对应表示多个遮罩图片。

## mask-clip

遮罩裁剪属性，与 background-clip 类似，但 mask-clip 多了对 svg 元素的支持，默认值为 border-box。Chrome 浏览器需要 `-webkit` 前缀。

mask-clip 可选值：

- content-box、padding-box、border-box、margin-box、no-clip 等所有元素有效的关键字;
- fill-box、stroke-box、view-box 等仅 svg 元素有效的关键字;

多值对应表示多个遮罩图片。

## mask-origin

遮罩源点属性，与 background-origin 类似，但 mask-origin 多了对 svg 元素的支持，默认值为 border-box。Chrome 浏览器需要 `-webkit` 前缀。

mask-clip 可选值：

- content-box、padding-box、border-box、margin-box、no-clip 等所有元素有效的关键字;
- fill-box、stroke-box、view-box 等仅 svg 元素有效的关键字;

多值对应表示多个遮罩图片。

## mask-type

遮罩类型属性，功能上和 mask-mode 一致，都是设置遮罩模式，但 mask-type 仅对 svg 的`<mask>`元素有效，因此除 IE 外所有浏览器都兼容。默认值为 luminance。

mask-type 可选值：

- luminance 亮度遮罩;
- alpha 透明度遮罩;

## mask-composite

遮罩混合属性，这个属性比较特殊，webkit 内核浏览器与 Firefox 浏览器取值完全不同。需要注意 mask-image 与 background-image 都支持多个图片，后书写的图片在底层，先书写的图片在顶层，因此，底层为 destination，顶层为 source。

这里很容易搞混，需要记住两个核心概念：

1. css 中 background-image 或 mask-image 等属性，先定义的图片在上层，后加载，后定义的图片在下层，先加载。详见[Image Sources: the background-image property](https://www.w3.org/TR/css-backgrounds-3/#the-background-image)。
2. canvas 中，源图像（source）是指将要添加到画布中的图像，目标图像（destination）是指之前已经添加到画布中的图像。详见[HTML canvas globalCompositeOperation Property - Definition and Usage](https://www.w3schools.com/tags/canvas_globalcompositeoperation.asp)。

结合这两个概念，就可以轻松得出：**先书写的图片，在顶层，后加载，是源图像。后书写的图片，在底层，先加载，是目标图像。**

下面所有示例中，红色为将要添加的图层 source，蓝色为已存在的图层 destination。

Firefox 浏览器，默认值为 add，可选值：

- add 表示遮罩相加，即 source（先书写的）在 destination（后书写的） 上叠加展示，相当于 webkit 支持属性中的 source-over;
- subtract 表示遮罩相减，即 source 与 destination 重合的地方不显示，仅保留其余部分的 source，相当于 webkit 支持属性中的 source-out;
- intersect 表示遮罩相交，即 source 与 destination 重合的地方才显示，相当于 webkit 支持属性中的 source-in;
- exclude 表示遮罩排除，即除 source 与 destination 重合以外的地方才显示，相当于 webkit 支持属性中的 xor;

webkit 内核浏览器，默认值为 source-over，借鉴了 Canvas 中的 globalCompositeOperation 属性值。可以参考[CanvasRenderingContext2D.globalCompositeOperation](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation)，可选值：

- clear 清除 source 和 destination 重合区域，仅显示 destination 与 source 不重合部分，和 destination-out 行为一致;
<canvas id="clear" width="100px" height="100px"></canvas>
<script>
function renderClear() {
  const canvas = document.getElementById("clear");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "clear";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderClear();
</script>

- copy 用 source 替换 destination，即只显示 source;
<canvas id="copy" width="100px" height="100px"></canvas>
<script>
function renderCopy() {
  const canvas = document.getElementById("copy");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "copy";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderCopy();
</script>

- source-over 表示 source 叠加在 destination 上显示，与 firefox 中的 add 属性行为一致;
<canvas id="source-over" width="100px" height="100px"></canvas>
<script>
function renderSourceOver() {
  const canvas = document.getElementById("source-over");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "source-over";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderSourceOver();
</script>

- source-in 表示仅显示在 destination 内的 source，即显示 source 与 destination 相交部分，注意显示主体为 source，与 firefox 中的 intersect 属性行为一致;
<canvas id="source-in" width="100px" height="100px"></canvas>
<script>
function renderSourceIn() {
  const canvas = document.getElementById("source-in");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "source-in";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderSourceIn();
</script>

- source-out 表示仅显示在 destination 外的 source，即显示 source 与 destination 不重合的部分，与 firefox 中的 substract 属性行为一致;
<canvas id="source-out" width="100px" height="100px"></canvas>
<script>
function renderSourceOut() {
  const canvas = document.getElementById("source-out");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "source-out";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderSourceOut();
</script>

- source-atop 表示在 destination 显示的基础上叠加显示 source 与 destination 重叠部分，注意重叠部分为 source;
<canvas id="source-atop" width="100px" height="100px"></canvas>
<script>
function renderSourceAtop() {
  const canvas = document.getElementById("source-atop");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "source-atop";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderSourceAtop();
</script>

- destination-over 表示 destination 叠加在 source 上显示;
<canvas id="destination-over" width="100px" height="100px"></canvas>
<script>
function renderDestinationOver() {
  const canvas = document.getElementById("destination-over");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "destination-over";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderDestinationOver();
</script>

- destination-in 表示仅显示在 source 内的 destination，即显示 destination 与 source 相交部分，注意显示主体为 destination;
<canvas id="destination-in" width="100px" height="100px"></canvas>
<script>
function renderDestinationIn() {
  const canvas = document.getElementById("destination-in");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "destination-in";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderDestinationIn();
</script>

- destination-out 表示仅显示在 source 外的 destination，即显示 destination 与 source 不重合部分;
<canvas id="destination-out" width="100px" height="100px"></canvas>
<script>
function renderDestinationOut() {
  const canvas = document.getElementById("destination-out");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "destination-out";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderDestinationOut();
</script>

- destination-atop 表示在 source 显示的基础上叠加显示 destination 与 source 重叠部分，注意重叠部分为 destination;
<canvas id="destination-atop" width="100px" height="100px"></canvas>
<script>
function renderDestinationAtop() {
  const canvas = document.getElementById("destination-atop");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "destination-atop";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderDestinationAtop();
</script>

- xor 表示 source 与 destination 重合部分不显示，显示两者不重合的部分，与 firefox 中的 exclude 行为一致;
<canvas id="xor" width="100px" height="100px"></canvas>
<script>
function renderXor() {
  const canvas = document.getElementById("xor");
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = "blue";
  ctx.fillRect(5, 5, 50, 50);
  ctx.globalCompositeOperation = "xor";
  ctx.fillStyle = 'red';
  ctx.fillRect(25, 25, 50, 50);
}
renderXor();
</script>

> 参考资料
>
> [客栈说书：CSS 遮罩 CSS3 mask/masks 详细介绍--张鑫旭](https://www.zhangxinxu.com/wordpress/2017/11/css-css3-mask-masks/)
>
> [多个背景的堆叠顺序](https://blog.csdn.net/cunqu9743/article/details/107001207)
>
> [Image Sources: the background-image property--w3](https://www.w3.org/TR/css-backgrounds-3/#the-background-image)
>
> [CanvasRenderingContext2D.globalCompositeOperation--MDN](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation)
>
> [-webkit-mask-composite--MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/-webkit-mask-composite)
>
> [mask-composite--MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/mask-composite)
>
> [mask-composite--css tricks](https://css-tricks.com/almanac/properties/m/mask-composite/)
>
> [Is it okay to just alias -webkit-mask-composite to mask-composite?](https://github.com/whatwg/compat/issues/153#issuecomment-891433638)
>
> [Compositing mask layers: the mask-composite property--w3](https://www.w3.org/TR/css-masking-1/#the-mask-composite)
>
> [HTML canvas globalCompositeOperation Property--w3schools](https://www.w3schools.com/tags/canvas_globalcompositeoperation.asp)
>
> [canvas API ，通俗的 canvas 基础知识（六）](https://www.cnblogs.com/liugang-vip/p/5443536.html)
