---
title: css布局flex缩写语法
date: 2023-02-12 14:15:38
tags:
  - flex
  - css
  - 缩写
categories: css
---

## 为什么推荐使用 flex 缩写语法

CSS 官方文档明确推荐 flex 属性使用缩写语法，因为单值属性所对应的计算值根据开发者日常使用进行了优化。按照以往经验，缩写语法缺省值应该使用默认值。但 flex 语法不同，单值缩写与双值缩写都是截然不同的计算结果。另外一个原因则是 flex 长语法有更高的理解成本，门槛较高，因此更推荐使用单值缩写语法。

## 使用 flex 缩写语法的场景

| 单值语法      | 等同于         | 备注       |
| ------------- | -------------- | ---------- |
| flex: initial | flex: 0 1 auto | 初始值常用 |
| flex: 0       | flex: 0 1 0%   | 适用场景少 |
| flex: none    | flex: 0 0 auto | 推荐       |
| flex: 1       | flex: 1 1 0%   | 推荐       |
| flex: auto    | flex: 1 1 auto | 适用场景少 |

<!-- more -->

### flex: initial

```css
flex: initial;
/* 等价于 */
flex: 0 1 auto;
/* 等价于 */
flex-grow: 0;
flex-shrink: 1;
flex-basis: auto;
```

即 flex 元素的父容器空间有剩余时，flex 元素尺寸不会增长；父容器尺寸不足时，元素尺寸会缩小；元素尺寸自适应于内容。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3</div>
</div>

<br />

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1测试1测试1</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2测试2测试2</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3测试3测试3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-initial {
    border: 1px solid green;
    padding: 2px 4px;
  }
</style>

<div class="wrapper">
  <div class="flex-initial">测试1</div>
  <div class="flex-initial">测试2</div>
  <div class="flex-initial">测试3</div>
</div>

<div class="wrapper">
  <div class="flex-initial">测试1测试1测试1</div>
  <div class="flex-initial">测试2测试2测试2</div>
  <div class="flex-initial">测试3测试3测试3</div>
</div>
```

由于`initial`是初始值，默认生效，因此日常开发不会特意书写，但并不代表使用场景不多。

`initial`适用于按钮、标题、小图标等小部件布局，小部件宽度通常都不会很宽，水平位置的控制使用`justify-content`和`margin-left: auto`或`margin-right: auto`实现。

<div style="
  display: flex;
  justify-content: space-between;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3</div>
</div>

<br />

<div style="
  display: flex;
  justify-content: space-between;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
    margin-left: auto
  ">测试2</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    justify-content: space-between;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-initial {
    border: 1px solid green;
    padding: 2px 4px;
  }
  .auto-left {
    margin-left: auto;
  }
</style>

<div class="wrapper">
  <div class="flex-initial">测试1</div>
  <div class="flex-initial">测试2</div>
  <div class="flex-initial">测试3</div>
</div>

<div class="wrapper">
  <div class="flex-initial">测试1</div>
  <div class="flex-initial auto-left">测试2</div>
  <div class="flex-initial">测试3</div>
</div>
```

### flex: 0

```css
flex: 0;
/* 等价于 */
flex: 0 1 0%;
/* 等价于 */
flex-grow: 0;
flex-shrink: 1;
flex-basis: 0%;
```

即元素尺寸会收缩，但不会扩张，固定分配数量为 0%。那么`flex: 0`元素的最终尺寸表现为最小内容宽度。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    flex: 0;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1测试1测试1</div>
  <div style="
    flex: 0;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2测试2测试2</div>
  <div style="
    flex: 0;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3测试3测试3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-0 {
    flex: 0;
    border: 1px solid green;
    padding: 2px 4px;
  }
</style>

<div class="wrapper">
  <div class="flex-0">测试1测试1测试1</div>
  <div class="flex-0">测试2测试2测试2</div>
  <div class="flex-0">测试3测试3测试3</div>
</div>
```

由于元素表现为最小内容宽度，因此适用`flex: 0`的场景并不多，除非元素内容主体是替换元素，如图像等，可以保证宽度永远都是图像宽度。

### flex: none

```css
flex: none;
/* 等价于 */
flex: 0 0 auto;
/* 等价于 */
flex-grow: 0;
flex-shrink: 0;
flex-basis: auto;
```

即元素尺寸即不会收缩，与不会扩张，元素尺寸由内容决定。此时元素不再有弹性，元素内容不会换行，元素最终尺寸表现为最大内容宽度。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    flex: none;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1测试1测试1</div>
  <div style="
    flex: none;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2测试2测试2</div>
  <div style="
    flex: none;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3测试3测试3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-none {
    flex: none;
    border: 1px solid green;
    padding: 2px 4px;
  }
</style>

<div class="wrapper">
  <div class="flex-none">测试1测试1测试1</div>
  <div class="flex-none">测试2测试2测试2</div>
  <div class="flex-none">测试3测试3测试3</div>
</div>
```

flex 元素宽度就是内容宽度，且内容不会换行，则适合使用`flex: none`。例如列表右侧常会有个操作按钮，按钮内容不能换行，适合使用`flex: none`。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">描述信息描述信息描述信息描述信息</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">按钮</div>
</div>

<br />

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">描述信息描述信息描述信息描述信息</div>
  <div style="
    flex: none;
    border: 1px solid green;
    padding: 2px 4px;
  ">按钮</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-initial {
    border: 1px solid green;
    padding: 2px 4px;
  }
  .flex-none {
    flex: none;
  }
</style>

<div class="wrapper">
  <div class="flex-initial">描述信息描述信息描述信息描述信息</div>
  <div class="flex-initial">按钮</div>
</div>

<div class="wrapper">
  <div class="flex-initial">描述信息描述信息描述信息描述信息</div>
  <div class="flex-initial flex-none">按钮</div>
</div>
```

### flex: 1

```css
flex: 1;
/* 等价于 */
flex: 1 1 0%;
/* 等价于 */
flex-grow: 1;
flex-shrink: 1;
flex-basis: 0%;
```

即元素尺寸可以弹性增大，也可以弹性减小，但在元素尺寸不足时优先最小化内容尺寸。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    flex: 1;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1测试1测试1</div>
  <div style="
    flex: 1;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2</div>
  <div style="
    flex: 1;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-1 {
    flex: 1;
    border: 1px solid green;
    padding: 2px 4px;
  }
</style>

<div class="wrapper">
  <div class="flex-1">测试1测试1测试1</div>
  <div class="flex-1">测试2</div>
  <div class="flex-1">测试3</div>
</div>
```

当希望元素充分利用剩余空间，但不会侵占其他元素应有宽度时，适合使用`flex: 1`，例如等分列表，等比例列表。之前`flex: none`的示例中，我们通过控制按钮元素`flex: none`实现按钮不换行，同样的效果也可以在描述信息元素上使用`flex: 1`来实现。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    flex: 1;
    border: 1px solid green;
    padding: 2px 4px;
  ">描述信息描述信息描述信息描述信息</div>
  <div style="
    border: 1px solid green;
    padding: 2px 4px;
  ">按钮</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-initial {
    border: 1px solid green;
    padding: 2px 4px;
  }
  .flex-1 {
    flex: 1;
  }
</style>

<div class="wrapper">
  <div class="flex-initial flex-1">描述信息描述信息描述信息描述信息</div>
  <div class="flex-initial">按钮</div>
</div>
```

### flex: auto

```css
flex: auto;
/* 等价于 */
flex: 1 1 auto;
/* 等价于 */
flex-grow: 1;
flex-shrink: 1;
flex-basis: auto;
```

即元素尺寸可以弹性变大，也可以弹性变小，在尺寸不足时，会优先最大化内容尺寸。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试1测试1测试1</div>
  <div style="
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试2</div>
  <div style="
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
  ">测试3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-auto {
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
  }
</style>

<div class="wrapper">
  <div class="flex-auto">测试1测试1测试1</div>
  <div class="flex-auto">测试2</div>
  <div class="flex-auto">测试3</div>
</div>
```

当希望元素能够充分利用剩余空间，但各自的尺寸按照各自的内容进行分配的时候，适合使用`flex: auto`。例如导航数量不固定，导航文字数量也不固定的情况。

<div style="
  display: flex;
  width: 200px;
  border: 1px dashed red;
">
  <div style="
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
    text-align: center;
  ">1</div>
  <div style="
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
    text-align: center;
  ">导航测试2</div>
  <div style="
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
    text-align: center;
  ">导航3</div>
</div>

<br />

```html
<style>
  .wrapper {
    display: flex;
    width: 200px;
    border: 1px dashed red;
  }
  .flex-auto {
    flex: auto;
    border: 1px solid green;
    padding: 2px 4px;
    text-align: center;
  }
</style>

<div class="wrapper">
  <div class="flex-auto">1</div>
  <div class="flex-auto">导航测试2</div>
  <div class="flex-auto">导航3</div>
</div>
```

> 参考资料
>
> [flex:0 flex:1 flex:none flex:auto 应该在什么场景下使用？](https://www.zhangxinxu.com/wordpress/2020/10/css-flex-0-1-none/)
