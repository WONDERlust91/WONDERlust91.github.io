---
title: iview一些组件无法绑定事件
date: 2018-12-06 13:09:46
tags:
- iview
- 事件
categories:
---

# iview无法绑定点击等事件

在使用iview过程中，我们会发现有时候在一些组件上监听click或其他事件时并没有按照我们预期的情况执行，这是因为被封装的组件在底层上可能并不是用一个可以绑定click事件的vue组件实现的，所以无法直接绑定click事件，这个时候我们可以用事件修饰符.native绑定原生事件。例如v-on:click.native，这样就可以绑定成功了。