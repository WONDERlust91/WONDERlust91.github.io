---
title: echarts环形图偏移时，graphic自定义文字无法居中
date: 2022-02-09 13:58:31
tags:
  - echarts
  - 环形图
  - 居中
categories: echarts
---

## 问题描述

使用环形图，且需要在环中写上自定义的文字，当环形图默认处于居中的位置时，文字位置也使用'center'配置，则可以保证正常居中。

```js
// 环形图默认居中时，自定义文字通过 left: 'center' 也很方便居中
// echarts配置
option = {
  graphic: [
    {
      type: "group",
      left: "center",
      top: "center",
      children: [
        {
          type: "text",
          style: {
            text: "测试居中文字",
            fontSize: 50,
          },
        },
      ],
    },
  ],
  series: [
    {
      type: "pie",
      radius: ["40%", "70%"],
      data: [
        { value: 1048, name: "Search Engine" },
        { value: 735, name: "Direct" },
        { value: 580, name: "Email" },
        { value: 484, name: "Union Ads" },
        { value: 300, name: "Video Ads" },
      ],
    },
  ],
};
```

而当环形图偏移放置时，自定义文字需要使用百分比定位，此时是文字的左边界定位于设置的百分比位置，而想要实现的效果是文字的中心定位于百分比设置的位置。

如果文字是静态的，还可以硬调整 left 位置，而如果文字是动态的，文字长度不定，硬调整就行不通了，还是需要实现居中效果。

<!-- more -->

```js
option = {
  graphic: [
    {
      type: "group",
      // 偏移设置需要居中的文字
      left: "35%", // 想要实现文字中心位于35%位置，但实际是文字左边位于35%位置。
      top: "center",
      children: [
        {
          type: "text",
          style: {
            text: "测试居中文字",
            fontSize: 50,
          },
        },
      ],
    },
  ],
  series: [
    {
      type: "pie",
      // 偏移位置放置饼图
      center: ["35%", "50%"],
      radius: ["40%", "70%"],
      data: [
        { value: 1048, name: "Search Engine" },
        { value: 735, name: "Direct" },
        { value: 580, name: "Email" },
        { value: 484, name: "Union Ads" },
        { value: 300, name: "Video Ads" },
      ],
    },
  ],
};
```

## 解决方案

1. 通过设置 graphic 的 bounding 属性为 `raw`，改变图形元素在定位时对自身包围盒的计算方式，默认 `all` 表示用自身及子节点整体经过 transform 变换后的包围盒进行定位。而 `raw` 表示仅使用自身（不包括子节点）的为未经 transform 变换的包围盒进行定位。

2. 在文字元素上，增加 textAlign 与 textVerticalAlign 的居中属性。

```js
option = {
  graphic: [
    {
      type: "group",
      left: "35%",
      top: "center",
      bounding: "raw", // bounding 改为 raw，否则下面对 textVerticalAlign 与 textAlign 的配置不会生效
      children: [
        {
          type: "text",
          style: {
            text: "测试居中文字",
            fontSize: 50,
            // 垂直居中
            textVerticalAlign: "middle",
            // 水平居中
            textAlign: "center",
          },
        },
      ],
    },
  ],
  series: [
    {
      type: "pie",
      center: ["35%", "50%"],
      radius: ["40%", "70%"],
      data: [
        { value: 1048, name: "Search Engine" },
        { value: 735, name: "Direct" },
        { value: 580, name: "Email" },
        { value: 484, name: "Union Ads" },
        { value: 300, name: "Video Ads" },
      ],
    },
  ],
};
```

> 参考资料
>
> [当环形图偏移时，graphic 自定义文字无法居中的问题](https://github.com/apache/echarts/issues/11889)
