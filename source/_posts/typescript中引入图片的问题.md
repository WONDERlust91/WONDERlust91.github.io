---
title: typescript中引入图片的问题
date: 2021-05-06 17:01:06
tags:
  - typescript
  - 图片
categories: typescript
---

## Typescript \*.ts 文件中引入图片时报错，没有正确的类型声明

webpack 都配置完成，但在 ts 文件中引入图片时报错，此时需要在全局 d.ts 文件中增加以下声明

```ts
// 全局d.ts文件中，增加声明
declare module "*.svg";
declare module "*.png";
declare module "*.jpg";
declare module "*.jpeg";
declare module "*.gif";
declare module "*.bmp";
declare module "*.tiff";

// 这样引入图片时就不会报类型错误了
import Img from "../xxx.png";
```
