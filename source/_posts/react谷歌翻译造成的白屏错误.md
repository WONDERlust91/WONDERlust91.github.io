---
title: react谷歌翻译造成的白屏错误
date: 2021-05-08 09:56:17
tags:
  - react
  - google translate
  - failed to execute "insertBefore" on "node"
categories: react
---

## react 白屏错误，报 failed to execute "insertBefore" on "node"

最近遇到个别客户在 Apple Chrome 环境下使用页面时，页面加载就白屏，报错 failed to execute "insertBefore" on "node"。

应急的解决办法：**对报错页面关闭谷歌翻译功能**

<!-- more -->

## 错误原因分析

此错误因 Google 翻译将页面中的 textNodes 替换为 font 标签，而 react 仍然保留了已经不存在于 DOM 树中的 textNodes 所致。

React 会在以下几种情况下抛出这个错误：

1. textNode 是条件渲染的且它并不是父节点的唯一子节点。当 textNode 被移除时，React 会调用 `parent.removeChild(textNode)`并抛出 textNode 不是父节点的子节点的错误。若是唯一子节点则不会抛出错误。

2. 某条件渲染的节点在 textNode 之前。React 会在节点插入时调用 parent.insertBefore(someNode, textNode)，这时就会因为 textNode 已经不是父节点的子节点而报错。

```tsx
// 情况 1
<div>
  {condition && 'Welcome'}
  <span>Something</span>
</div>

// 情况 2
<div>
  {condition && <span>Something</span>}
  Welcome
</div>

// 不报错的情况
<div>
  {condition && 'Welcome'}
</div>
```

## 解决方案

基于问题产生的原因，只要避免上述条件就可以避免这些问题。最简单的解决方案就是将 textNode 包裹在 span 标签内，这样即使被 React 引用的节点被替换为 font 标签，它们依然保留在 DOM 树中。

```tsx
// 情况 1 解决方案
<div>
  {condition && <span>Welcome</span>}
  <span>Something</span>
</div>

// 情况 2 解决方案
<div>
  {condition && <span>Something</span>}
  <span>Welcome</span>
</div>
```

> [参考资料](https://github.com/facebook/react/issues/11538#issuecomment-390386520)
