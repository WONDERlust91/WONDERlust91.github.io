---
title: 微信小程序加载echarts图表报错无法从undefined上读取createView属性
date: 2021-06-07 09:56:30
tags:
  - 微信小程序
  - echarts
categories: 微信小程序
---

## 问题描述

小程序中按 echarts 官方文档引入 echarts，而后将 web 端 echarts 图表的配置（setOption 中需要的 options 对象）复制过来使用，发现小程序开发工具中可以正常加载图表，但一使用预览，就无法加载图表，而在预览下打开 vConsole 调试，直接闪退；使用真机调试，直接报错`Cannot not read property 'createView' of undefined`。

## 微信开放社区讨论

在微信开放社区查找相关错误，发现社区中关于真机调试报`Cannot not read property 'createView' of undefined`错误的原因是真机调试不支持 canvas 2d，但预览模式是没问题的，故判断无法加载图表并闪退并非因为`Cannot not read property 'createView' of undefined`错误造成。

## 排除错误

对比 echarts 官方示例，发现 echarts 官方示例加载完全没有问题，这时才开始怀疑是配置对象造成了加载失败（一直没有怀疑是因为 web 端加载无误，开发工具中加载也无误）。逐个调试配置对象，终于发现配置对象中，对颜色的定义有一行为`rgb(20 194 59)`，rgb 值中间未使用逗号分隔，这是 CSS4 的标准，而小程序并不支持 CSS4 标准的 color，用逗号分隔后，就没有问题了。
