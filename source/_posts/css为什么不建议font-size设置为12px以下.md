---
title: css为什么不建议font-size设置为12px以下
date: 2021-09-01 09:48:05
tags:
  - css
  - font-size
categories: css
---

## 现象

在业务中发现设置小于 12px 的字号不生效，最小只能设置为 12px 的问题。

## 结论

由于中文字体笔划较密，小于 12px 时，展示效果不佳，故 chrome 浏览器中文版默认最小字号为 12px，英文版则没有最小字号为 12px 的限制。为了有更为统一的用户体验，而无需用户自行修改浏览器默认配置，不使用小于 12px 的字号，是最佳实践。

## 修改最小字号

如果一定要修改最小字号，地址栏输入`chorme://settings/fonts`，进入字体设置，调整最小字号为 0，即可使小于 12px 的字号生效了。

> 参考资料
>
> [「每日一题」为什么不建议将 font-size 设置为 12px 以下？](https://zhuanlan.zhihu.com/p/22374961)
>
> [再谈 Chrome 的最小字体 12px 限制](https://zhuanlan.zhihu.com/p/69695071)
