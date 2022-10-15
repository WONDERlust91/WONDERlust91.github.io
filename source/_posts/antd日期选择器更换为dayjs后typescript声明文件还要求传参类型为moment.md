---
title: antd日期选择器更换为dayjs后typescript声明文件还要求传参类型为moment
date: 2021-06-09 14:34:26
tags:
  - 日期选择器
  - antd
categories: antd
---

## 问题背景

由于 moment 时间库过大，且不再维护，故想要将之替换为 dayjs 库，antd 官方文档中也提供了两种方式去替换。使用 antd-dayjs-webpack-plugin 这种替换方式，无需使用自定义的日期选择器组件，故选择该方式。

真实开发场景在 Typescript 环境下使用时，发现传参要求还是 moment 类型，与 dayjs 类型不一致。

<!-- more -->

## 解决方案

官方文档对 antd-dayjs-webpack-plugin 方式说明不详细，实际上这种方式是基于 moment 与 dayjs 接口一致的条件下，在编译阶段将 moment 替换为 dayjs 的，而 antd 关于日期的 ts 类型并未改变。故而书写业务逻辑代码时，还是应该使用 moment，或者覆盖 antd 中的类型。而若要完全脱离 moment 库在业务逻辑代码中使用 dayjs，则必需要使用第一种方式，即自定义日期选择器的形式。

我最终还是在代码中使用了 moment 而没有覆盖 antd 类型，覆盖类型不优雅，而自定义组件放在框架中不方便，他人使用总是习惯从 antd 中引入，而不是自定义组件中。

```ts
// 覆盖antd类型
declare module "moment" {
  import { Dayjs } from "dayjs";
  namespace moment {
    type Moment = Dayjs;
  }
  export = moment;
  export as namespace moment;
}
```

```js
// 自定义日期选择器
import dayjsGenerateConfig from "rc-picker/es/generate/dayjs";
import generatePicker from "antd/es/date-picker/generatePicker";
import "antd/es/date-picker/style/index";

const DatePicker = generatePicker(dayjsGenerateConfig);

export default DatePicker;
```

> 参考资料
>
> [antd-dayjs-webpack-plugin 插件替换 moment 之后，TypeScript 声明文件还是要求传参类型为 moment.Moment](https://github.com/ant-design/antd-dayjs-webpack-plugin/issues/23)
>
> [使用 Dayjs 替换 Moment 自定义 DatePicker 后，组件无法国际化](https://github.com/ant-design/ant-design/issues/27276)
>
> [自定义日期选择器代码示例](https://codesandbox.io/s/wentifuxiandemo-forked-l3704?file=/index.js)
