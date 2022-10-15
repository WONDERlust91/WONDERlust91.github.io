---
title: leaflet防止矢量图层在html2canvas截图时偏移
date: 2022-01-14 21:03:55
tags:
  - leaflet
  - html2canvas
  - 截图偏移
categories: leaflet
---

在使用 html2canvas 截图时发现 leaflet 中的 geojson 或 polygon 图层在截图后出现偏移。

查阅[html2canvas 官方文档](https://html2canvas.hertzen.com/features)看到，html2canvas 对 transform 属性只是有限支持，而在[leaflet 官方文档](https://leafletjs.com/reference.html#map-option)中，初始化地图时的 map-options 中的 renderer 属性规定了矢量图层（vector layer）默认的渲染方式，根据浏览器的支持情况默认选择 svg 与 canvas 两种方式，两种都支持时优先选择 svg 方式。

geojson 的矢量图层，使用 svg 方式在地图上定位就会用到 CSS 的 transform 属性，这也是最终导致 html2canvas 截图漂移的原因。

若将 map-options 中的 renderer 属性固定为 L.Canvas()，则默认使用 canvas 来绘制矢量图层，就避免了使用 svg 标签再通过 transform 属性定位的问题，即可正常截图。

> 参考资料
>
> [html2canvas 官方文档](https://html2canvas.hertzen.com/features)
>
> [leaflet 官方文档](https://leafletjs.com/reference.html#map-option)
>
> [Problem taking the screenshot of a Leaflet polygon location](https://github.com/niklasvh/html2canvas/issues/2676)
