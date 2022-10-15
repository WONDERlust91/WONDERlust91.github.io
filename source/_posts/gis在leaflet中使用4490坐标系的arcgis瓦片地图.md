---
title: gis在leaflet中使用4490坐标系的arcgis瓦片地图
date: 2021-06-24 14:19:17
tags:
  - gis
  - leaflet
  - 4490
  - arcgis
categories: gis
---

## 背景情况

在对接客户过程中，客户不使用天地图作为地图服务，而要求使用他们发布好的 ArcGIS Server 离线瓦片地图，ArcGIS 离线地图的 z、x、y 规则与 leaflet 地图引擎的默认解析规则不一致，故需要扩展 leaflet 的 TileLayer。

## 使用 ArcGIS 瓦片地图

### leaflet 解析规则

leaflet 的 TileLayer 通过内部的 getTileUrl 回调方法去获取瓦片路径，getTileUrl 会传入一个对象，对象中包含地图层级 z，瓦片横坐标 x，瓦片纵坐标 y。这些数据都为十进制数，与 ArcGIS 的瓦片解析规则不一致。

### ArcGIS 瓦片路径

ArcGIS Server 切片后路径规则如下：

- \_\_alllayers
  - L00
    - R00000000
      - C00000000.png
      - C0000000f.png
    - R0000001f
  - L21
  - Conf.xml

可以看出 ArcGIS Server 切片规则：

1. 最外层为\_\_alllayers 文件夹;
2. L 为地图层级文件夹，固定为 L 字母加两位十进制数，0-9 这种不足两位的层级，前面补 0;
3. R 为瓦片纵坐标，固定为 R 字母加八位十六进制数，不足八位的，前面补 0;
4. C 为瓦片横坐标，固定为 C 字母加八位十六进制数，不足八位的，前面补 0;
5. Conf.xml 为配置文件，包含了瓦片的投影方式，原点坐标，分辨率等参数;

<!-- more -->

### 扩展 leaflet.TileLayer

了解了 leaflet 解析规则与 ArcGIS 瓦片规则的差别，就可以扩展 leaflet.TileLayer 类，自定义瓦片解析规则了。

```ts
// 瓦片加载扩展类
const ArcGisServerTileLoad = L.TileLayer.extend({
  // 扩展L.TileLayer类
  getTileUrl: function (tilePoint) {
    // 地图层级
    let zz = tilePoint.z.toString();
    // 添加字母L并补0
    zz = "L" + "00".slice(0, 2 - zz.length) + zz;
    // 初始8位16进制数
    const initCR = "00000000";
    // 瓦片横坐标，10进制转16进制
    let xx = tilePoint.x.toString(16);
    // 添加字母并补0
    xx = "C" + initCR.substring(0, 8 - xx.length) + xx;
    // 瓦片纵坐标，10进制转16进制
    let yy = tilePoint.y.toString(16);
    // 添加字母并补0
    yy = "R" + initCR.substring(0, 8 - yy.length) + yy;
    // 使用leaflet模板将各变量转换为路径字符串并返回
    return L.Util.template(
      this._url,
      L.extend(
        {
          s: this._getSubdomain(tilePoint),
          z: zz,
          x: xx,
          y: yy,
        },
        this.options
      )
    );
  },
});
// 生成瓦片实例函数
const arcGisServerTileLayer = (url: string, options?: L.TileLayerOptions) => {
  return new ArcGisServerTileLoad(url, options);
};
// 使用ArcGIS瓦片
// 注意这里顺序为z、y、x，而不是z、x、y；map为leaflet的map实例
arcGisServerTileLayer("http://www.map-example.com/__alllayers/{z}/{y}/{x}.png").addTo(map);
```

## 坐标系投影

将瓦片解析规则调整为 ArcGIS 的解析规则后，发现瓦片地图仍然无法加载，排查原因后发现是瓦片的投影坐标系与 leaflet 地图引擎投影坐标系不同造成的。瓦片使用了 2000 国家大地坐标系，而 leaflet 默认只有 4326 与 3857 两种默认投影。

### 相关概念

- EPSG：European Petroleum Survey Group，欧洲石油调查组织，最初由 EPSG 在 1985 年创建了地理参数数据集，并在 1993 年公开。2005 年 EPSG 并入国际油气生产者协会（IOGP），并入 IOGP 后，为避免混淆，数据集仍延用 EPSG 的名称，并由 IOGP 维护，后来 EPSG 就更多代指该数据集名称了。EPSG 编码介于 1024 至 32767 之间，每种地图投影都被分配了一个编码，该编码作为一种众所周知的机器可读的标准化文本存在，如`EPSG: 4326`。EPSG 数据集可以通过网站[https:epsg.io](https:epsg.io)查看。

- EPSG:4326 - WGS 84（world geodetic system 1984）即 世界大地测量系统 1984，广泛用于 GPS 定位等;

- EPSG:3857 - Web Mercator Projection 网络墨卡托投影，常用于网络地图，如 Google Maps、OpenStreetMap;

- EPSG:7789 - International Terrestrial Reference Frame 2014（ITRF2014）即 国际陆地参考系 2014;

- EPSG:4490 - 2000 国家大地坐标系;

### leaflet 使用 EPSG:4490 投影

- 安装并引入 proj4leaflet 插件;

```shell
npm install proj4leaflet;
```

```js
// 如果有引入leaflet库，引入proj4leaflet后，会自动挂载为L.Proj
import "proj4leaflet";
```

- 通过[https://epsg.io]网站查询 4490 投影定义配置，下滑页面至导出功能，复制 Proj4js 版本的 4490 定义配置;

```js
proj4.defs("EPSG:4490", "+proj=longlat +ellps=GRS80 +no_defs");
```

- 查看瓦片目录下的 Conf.xml 文件，找到投影坐标系、原点信息、分辨率等信息;

```xml
<!-- WKT标签表示了投影的坐标系，用于在epsg.io网站上查询对应的epsg编码，例如该WKT表示EPSG:4490 -->
<WKT>GEOGCS["CGCS_2000",DATUM["D_2000",SPHEROID["S_2000",6378137.0,298.2572221010041]],PRIMEM["Greenwich",0.0],UNIT["Degree",0.0174532925199433]]</WKT>
<!-- TileOrigin标签表示瓦片原点信息 -->
<TileOrigin xsi:type="typens:PointN">
  <X>-180</X>
  <Y>90</Y>
</TileOrigin>
<!-- TileCols与TileRows标签表示瓦片大小 -->
<TileCols>256</TileCols>
<TileRows>256</TileRows>
<!-- LODInfo中代表每个地图层级的信息 -->
<LODInfo xsi:type="typens:LODInfo">
  <!-- LevelID 地图层级 -->
  <LevelID>0</LevelID>
  <!-- Scale 缩放 -->
  <Scale>73957338.863641396</Scale>
  <!-- Resolution 分辨率 -->
  <Resolution>0.17597860392101103</Resolution>
</LODInfo>
```

- 根据上述信息，并使用 proj4leaflet 创建新的投影

```ts
// 使用EPSG网站上复制出的4490配置
const EPSG4490 = new L.Proj.CRS("EPSG:4490", "+proj=longlat +ellps=GRS80 +no_defs", {
  // 使用Conf.xml中的resolution数据
  resolutions: [
    0.17597860392101103, 0.087989301960505514, 0.043994650980252757, 0.021997325490126379, 0.010998662745063191,
    0.0054993313725315955, 0.0027496656862657978, 0.0013748328431328989, 0.00068741642156644944,
  ],
  // 使用Conf.xml中的origin数据
  origin: [-180, 90],
});

// 加载地图时，使用4490投影
L.map("map", {
  crs: EPSG4490,
});
```
