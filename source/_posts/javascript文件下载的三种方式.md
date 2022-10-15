---
title: javascript文件下载的三种方式
date: 2019-03-24 21:57:54
tags:
  - javascript
  - 文件下载
categories: javascript
---

## 使用全局的 window 对象

文件下载一般直接访问文件的静态服务器地址即可，故可以使用 window.location.href 与 window.open 来实现下载，这种方法可能会受到浏览禁止弹窗的影响

```javascript
window.location.href = "www.test.com/download/test.pdf";
// 或者使用window.open()
window.open("www.test.com/download/test.pdf");
```

## 使用 a 标签与链接地址

这种方式兼容性最好，一般应该使用这种方式

```javascript
// 创建a标签
const ele = document.createElement("a");
// 设置download属性使浏览器能打开的文件不再默认打开而是下载，也可以设置下载文件名
ele.download = "export";
// 设置a标签链接，可以使用queryString传参
ele.href = "www.test.com/download/test.excel?id=1&name=2";
// 模拟鼠标点击a标签
ele.click();
```

## 使用 ajax 与 a 标签

这种方式不兼容 IE，因为 IE 对 blob 等支持不全。需要注意的是，请求时还要设置 responseType 为 blob，否则接收二进制流时会遇到乱码问题。

```javascript
// 请求下载接口
axios({
  url: "www.test.com/download/",
  method: "post",
  data: {
    id: 0,
    name: 1,
  },
  responseType: "blob",
})
  .then((res) => {
    // 获取文件数据后，创建a标签
    const ele = document.createElement("a");
    // 设置download属性
    ele.download = "export";
    // 使用blob接收文件，注意要设置type，否则文件为乱码，data应该放在数组中（arrayBuffer）
    // 2003版 后缀为xls的文件type值为application/vnd.ms-excel
    // 2007版 后缀为xlsx的文件type值为application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
    const blob = new Blob([res.data], { type: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" });
    // 使用blob创建访问链接
    ele.href = URL.createObjectURL(blob);
    // 模拟鼠标点击
    ele.click();
    // 释放访问链接
    URL.revokeObjectURL(ele.href);
  })
  .catch((err) => console.error(err));
```
