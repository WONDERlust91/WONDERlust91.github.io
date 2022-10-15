---
title: 微信小程序scroll-view与windowHeight
date: 2018-10-21 23:06:44
tags: 
- 微信小程序
- scroll-view
- windowHeight
categories: 微信小程序
---

# 微信小程序中实现菜单栏上滑至顶后，固定位置

1. 小程序中只能使用scroll-view组件监听页面滚动离顶部的距离，scroll-view的滑动会与page页面的滑动相叠加。因此需要设置scroll-view组件的宽高分别为可用窗口的实际高度。 不应该在程序加载时通过接口getSystemInfo获取可用窗口高度windowHeight，因为此时的窗口是不包含底部tabbar高度的窗口，而跳转后的页面无tabbar，高度包含了tabbar高度，因此要在跳转后的页面onReady生命周期函数中使用getSystemInfo获取windowHeight。

2. 在scroll-view中监听离顶部的距离，应该避免实时赋值操作，因为setData函数与监听函数都很消耗性能，应在监听函数中做完判断再进行赋值，否则会造成真机预览时滑动卡顿的状况。

3. 由于使用了flex布局当菜单栏吸顶变为绝对定位时，应该用一个和菜单栏相同大小的空view组件填补菜单栏的位置，否则会发生下面布局整体上移的现象。