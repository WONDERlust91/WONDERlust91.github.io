---
title: 微信小程序封装echarts组件
date: 2022-04-29 22:36:45
tags:
  - ec-canvas
  - echarts
  - 微信小程序
categories: 微信小程序
---

## echarts 在微信小程序

由于微信小程序没有浏览器端的 DOM，故 PC 端的 echarts 无法直接在微信小程序中使用，echarts 官方基于微信小程序，开发了小程序版的 echarts 组件 ec-canvas，ec-canvas 迭代相对 echarts 主库较慢，不过支持替换 echarts.js 文件来实现更新。

ec-canvas 组件更新较慢，截止目前(2022-04)，[echarts-for-weixin](https://github.com/ecomfe/echarts-for-weixin)最新版本为 2.0.0，其中 echarts 版本为 5.1.1，而[echarts](https://github.com/apache/echarts)主库已更新至 5.3.2 版本。

ec-canvas 组件考虑了通用性，但是使用并不是很方便，且真实场景多是 lazyLoad，只要改变数据，不需要多次新建或销毁实例，故在 ec-canvas 基础上再封装一次，会更方便使用。

前置准备：

- 从 echarts-for-weixin 项目中复制 ec-canvas 文件夹到小程序项目中，通常习惯将全局通用的组件放在项目根目录下的组件目录中，即/components

<!-- more -->

## ec-canvas 组件改动

若不开启微信小程序 ES6 转码为 ES5，则需要将 ec-canvas 中代码 ESM 引入方式改为 CJS 方式，若开启则无需修改下面两个文件。

- ec-canvas/ec-canvas.js

```js
// 原
import WxCanvas from "./wx-canvas";
import * as echarts from "./echarts";
// 改为
const WxCanvas = require("./wx-canvas");
const echarts = require("./echarts");
```

- ec-canvas/wx-canvas.js

```js
// 原
export default class WxCanvas {}
// 改为
module.exports = class WxCanvas {};
```

若需要更新 echarts 为最新版本，还需要对 echarts 进行修改，否则会报错。若不用 echarts 最新特性，则使用 ec-canvas 中默认的 echarts 即可。

- ec-canvas/echarts.js（替换为 echarts 主库中最新的 echarts，5.3.2）

```js
// 全局搜索 t.addEventListener
t.addEventListener(e, n, i);
// 替换为
t.addEventListener && t.addEventListener(e, n, i);

// 全局搜索 t.preventDefault()
t.preventDefault();
// 替换为
t.preventDefault && t.preventDefault();

// 全局搜索 t.stopPropagation()
t.stopPropagation();
// 替换为
t.stopPropagation && t.stopPropagation();
```

## 使用 ec-canvas 封装 echarts-component

使用微信开发工具，新建文件夹，并新建组件，命名为 echarts-component

- echarts-component.js

```js
// components/echarts-component/echarts-component.js
const echarts = require("/components/ec-canvas/echarts");
Component({
  /**
   * 组件的属性列表
   */
  properties: {
    // 传入canvasId
    canvasId: String,
    // 传入echarts的配置项
    options: Object,
  },

  /**
   * 组件的初始数据
   */
  data: {
    ec: {
      // 懒加载方式，即先生成echarts实例，后续再setOption
      lazyLoad: true,
    },
  },

  /**
   * 组件的方法列表
   */
  methods: {
    // 初始化图表
    init(options) {
      this._ecComponent.init((canvas, width, height, dpr) => {
        const chart = echarts.init(canvas, null, {
          width,
          height,
          devicePixelRatio: dpr,
        });
        chart.setOption(options);
        this._chart = chart;
        return chart;
      });
    },
    // 设置图表配置项
    setOption(options) {
      if (this._chart) {
        this._chart.setOption(options, { notMerge: true });
      }
    },
    // 销毁图表实例
    dispose() {
      if (this._chart) {
        this._chart.dispose();
      }
    },
  },
  // 生命周期
  lifetimes: {
    attached() {},
    ready() {
      // 组件准备就绪，获取组件
      this._ecComponent = this.selectComponent(`#${this.data.canvasId}`);
      // 初始化图表
      this.init(this.data.options);
    },
    detached() {
      // 组件卸载，销毁图表实例
      this.dispose();
    },
  },
  // 监听器
  observers: {
    // 监听图表配置项改变，设置图表配置项
    options: function (options) {
      this.setOption(options);
    },
  },
  // 导出图表实例（按微信文档这样导出的是图表实例，但不知为何，实际导出是组件实例，即this）
  export() {
    return this._chart;
  },
});
```

- echarts-component.json

```json
{
  "component": true,
  "usingComponents": {
    "ec-canvas": "/components/ec-canvas/ec-canvas"
  }
}
```

- echarts-component.wxml

```html
<!--components/echarts-component/echarts-component.wxml-->
<ec-canvas
  class="echarts-component"
  id="{{canvasId}}"
  canvas-id="{{canvasId}}"
  ec="{{ec}}"
></ec-canvas>
```

- echarts-component.wxss

```css
/* components/echarts-component/echarts-component.wxss */
.echarts-component {
  width: 100%;
  height: 100%;
}
```

## 使用封装好的 echarts-components

在 index 页使用示例

- index.js

```js
let chartInstance = null;

Page({
  data: {
    chartOption: {
      title: { text: "暂无数据", left: "center", top: "center" },
    },
  },
  onReady() {
    // 通常为获取数据后，改变图表配置项
    this.setData({
      chartOption: {
        xAxis: {
          type: "category",
          data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: [150, 230, 224, 218, 135, 147, 260],
            type: "line",
          },
        ],
      },
    });
    // 若无操作图表或监听图表事件的需要，则无需获取图表实例，直接修改配置项即可
    // 本次更新结束时，获取实例，防止图表初始化未完成
    wx.nextTick(() => {
      // 获取组件实例
      const componentInstance = this.selecteComponent(".index-chart");
      // 获取图表实例，组件中使用_chart作为图表实例
      chartInstance = componentInstance && componentInstance._chart;
      // 监听图表事件
      chartInstance &&
        chartInstance.on("highlight", (params) => {
          // 图表事件触发要执行的逻辑
        });
      // 分发图表操作
      chartInstance &&
        chartInstance.dispatchAction({
          type: "highlight",
          seriesIndex: 0,
          dataIndex: 0,
        });
    });
  },
});
```

- index.json

```json
{
  "usingComponents": {
    "echarts-component": "/components/echarts-component/echarts-component"
  }
}
```

- index.wxml

```html
<view class="index">
  <echarts-component
    class="index-chart"
    canvas-id="your-canvas-id"
    options="{{chartOption}}"
  ></echarts-component>
</view>
```

- index.wxss

```css
.index {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.index-chart {
  width: 100%;
  height: 50%;
}
```

> 参考资料
>
> [在微信小程序中使用 ECharts](https://echarts.apache.org/handbook/zh/how-to/cross-platform/wechat-app)
>
> [微信官方文档·小程序-自定义组件](https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/)
