---
title: html使用svg的几种方式
date: 2022-02-07 10:11:45
tags:
  - svg
  - html
categories: html
---

SVG 是一种矢量图形的图片格式，是 Scalable Vector Graphics（可缩放矢量图形）的缩写。

SVG 的优点：

- 更小的文件体积，更高的压缩率
- 可缩放到任意大小，而不会失真（除缩放至很小的尺寸外）
- 可改变为任何颜色

## SVG 的使用方式

### 作为图片使用

SVG 可以像普通图片那样在 img 标签和 css 的 background 中使用，但是以这种方式使用 SVG 也无法实现对 SVG 的颜色进行修改的操作。

```html
<img src="xxx.svg" alt="xxx" />
```

```html
<div class="xxx">xxx</div>

<style>
  .xxx {
    width: 100px;
    height: 80px;
    background-image: url(xxx.svg);
  }
</style>
```

<!-- more -->

### 作为 object、embed、iframe 资源引入

尽量不要在 embed 和 iframe 标签中使用 svg。embed 标签曾广泛应用于 java 插件、Flash 插件的注入，但如今因安全问题等原因，java、Flash 插件都慢慢被浏览器禁用，虽然各浏览器厂商都支持在 embed 标签中使用 SVG，但不是明确的标准，尽量不要使用；而 iframe 标签，不利于缓存图片，也无法进行浏览器的 SEO 优化。

以 object 标签为例，引入 SVG 图片。网上有大量文章指出使用 object 标签引入的 SVG 可以获取 DOM，被操作。在 Chrome 浏览器实测发现 contentElement 属性、getSVGDocument()方法都返回 null，暂未查到原因。

```html
<object id="svg" data="xxx.svg" type="image/svg+xml"></object>

<script>
  // 网络文章指出可以如下方式操作 SVG，实测无效，但暂时列在这里
  const svgObject = document.getElementById("svg");
  svgObject.addEventListener("load", () => {
    const svgDom = svgObject.getSVGDocument(); // chrome 下返回 null
    // const svgDom = svgObject.contentElement; // chrome 下返回 undefined
    const rect = svgDom.querySelector("rect");
    rect.setAAttribute("fill", "green");
  });
</script>
```

### 作为行内 SVG 引入

可以直接将 SVG 代码放入 HTML 文档中，这样最大的便捷是可以非常容易地使用 css 操作 SVG 的颜色，但这也丧失了 SVG 作为图片的可缓存性与 SEO，他人想直接引用这个 SVG 图片也变得不那么方便了。

```html
<body>
  <svg xmlns="http://www.w3.org/2000/svg">
    <text x="10" y="50" font-size="30">My SVG</text>
  </svg>
</body>
```

### 以 Data URI 方式引入

另一个使用 SVG 的方式是将 SVG 转换为 Data URI 的形式。这无法缩减文件的体积，但减少了请求和网络资源消耗。

```html
<!-- Data URI 以 img 标签形式 -->
<img src="data:image/svg+xml;base64,[data]" />

<!-- Data URI 以 css 形式 -->
<div class="svg"></div>
<style>
  .svg {
    background: url("data:image/svg+xml;base64,[data]");
  }
</style>

<!-- Data URI 以 object 标签形式 -->
<object type="image/svg+xml" data="data:image/svg+xml;base64,[data]">
  fallback
</object>
```

当然以 base64 形式编码会牺牲代码可读性，大量的 base64 代码在文件中，翻几页都翻不完。事实上还可以使用其他格式的 Data URI，在 SVG 的应用场景下，不建议使用 base64 编码的方式。

```html
<!-- base64 -->
data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovl...

<!-- UTF-8, not encoded -->
data:image/svg+xml;chartset=UTF-8,<svg ...>...</svg>

<!-- UTF-8, optimized encoding for compatibility -->
data:image/svg+xml;charset=UTF-8,%3Csvg xmlns="http://..." ...

<!-- Fully URL encoded ASCII -->
data:image/svg+xml;chartset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A//...
```

## 最佳实践

前面所列举的 SVG 使用方式，都或多或少地存在缺点，内联 SVG 用法，是最方便操作 SVG 的方式了，但是每次都复制一遍 SVG 代码不够优雅。这一步实际上可以交给构建工具来完成，[@svgr/webpack](https://www.npmjs.com/package/@svgr/webpack)这个 webpack-loader 实现了自动导入 SVG。

以 React 为例，React 的[CRA](https://create-react-app.dev/docs/adding-images-fonts-and-files#adding-svgs)脚手架内部已使用 svgr 实现了导入 SVG 的功能。

```jsx
// 注意，必需要加大括号和 ReactComponent，否则就没有用到 svgr 的 loader，而是使用了 url-loader，导入了 svg 的地址。
import { ReactComponent as Logo } from "./logo.svg";

function App() {
  return (
    // 将 logo SVG 的颜色改为紫色
    <div style={{ fill: "purple" }}>
      <Logo />
    </div>
  );
}
```

## SVG 内联图标颜色

### fill 属性

在 CSS 中使用 `fill` 属性，即可修改 SVG 的颜色，就如同字体的 `color` 一样。

```css
.svg {
  fill: #369;
}
```

如果你使用 `fill` 属性发现无法改变颜色，需要检查下 SVG 中是否已经写入了 `fill` 属性，与 UI 同事沟通，给出 SVG 文件时，不要添加 `fill` 属性。

### 颜色继承

祖先节点 `fill` 属性设置颜色后，如果没有其他 `fill` 干扰，祖先节点下的所有 SVG 图标颜色都为祖先节点设计的颜色。

```css
body {
  fill: #369;
}
```

### 当前色 currentColor

`currentColor` 指当前元素所处环境的文字颜色，例如：

```css
li {
  color: #369;
}
li > svg {
  fill: currentColor;
}
```

### 渐变色

CSS 中的 `fill` 属性，不仅可以跟随颜色值，还能是页面上的 SVG 元素，当然也可以是渐变 SVG 元素。

例如，只要页面中存在如下 SVG 代码：

```html
<svg width="0" height="0">
  <defs>
    <linearGradient x1="0" y1="0" x2="1" y2="1" id="lineGradient">
      <stop offset="0%" stop-color="#000000">
    </linearGradient>
  </defs>
</svg>
```

就可以在 CSS 中引用：

```css
.svg {
  fill: url(#lineGradient);
}
```

## 注意事项

### SVG 变形

使用圆形图标时，发现图标在设计稿中是圆的，但在浏览器中不圆。通常这种情况因为图标中的 path 路径值不是整数造成的。SVG 原生尺寸小，再加上压缩后，可能导致小数位数改变，图形失真。使用较大的原始尺寸，再注意 path 尽量取整，就可以避免该问题。

### SVG 绑定事件无效

一些浏览器中，直接在 SVG 组件上绑定 click 事件，事件不会触发。需要在 svg 标签外再套一层其他可稳定触发事件的标签如 i、button、div 等。

### SVG 颜色只改变了部分

这通常由于 SVG 图标中，同时出现了 fill，stroke 两种类型的 path，需要 UI 同事在设计工具中，将图标先轮廓化（outline stroke），再扁平化（flatten）。

### SVG 边缘不完整

这通常由于 UI 提供的 SVG 贴边了，贴边位置又不是完整的像素，只需要将 SVG 各边都至少保留 1px 的空白即可。

> 参考资料
>
> [SVG 图标颜色文字般继承与填充](https://www.zhangxinxu.com/wordpress/2014/07/svg-sprites-fill-color-currentcolor/)
>
> [【非标题党】SVG 图标看我就够了](https://zhuanlan.zhihu.com/p/258796445)
>
> [Using SVG](https://css-tricks.com/using-svg/)
>
> [The Best Way to Embed SVG on HTML (2021)](https://vecta.io/blog/best-way-to-embed-svg)
>
> [Do I use \<img\>, \<object\>, or \<embed\> for SVG files?](https://stackoverflow.com/questions/4476526/do-i-use-img-object-or-embed-for-svg-files)
