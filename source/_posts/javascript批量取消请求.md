---
title: javascript批量取消请求
date: 2021-05-06 14:24:01
tags:
  - javascript
  - 取消请求
  - fetch
  - axios
categories: javascript
---

前端框架时代，越来越多的网站成为了单页应用，路由被前端接管，切换到新的路由，只要请求 json 数据就可以了，但随之而来，也产生了一个问题：上一个路由发出的请求还未收到响应，下一个路由又发了新的请求，而旧请求响应在新请求的响应后才到达，就会发生本应该使用新响应数据的视图却最终使用了旧响应的数据，导致内容文不对题的情况发生。

解决这个问题的思路乍一想也很简单，在路由切换时，取消所有尚未返回响应的请求即可，但现实并非如此。

<!-- more -->

## fetch 取消请求

fetch 现阶段的稳定标准尚不支持取消请求，试验性提案中有一个 signal 配置属性，配合同样处于试验性提案阶段的[AbortController](https://developer.mozilla.org/zh-CN/docs/Web/API/AbortController) API 可以实现取消请求的效果。(主流浏览器基本均已支持该试验提案)

取消 fetch 调用的示例：

```javascript
// 创建一个 AbortController 实例，该实例具有 signal 属性
const controller = new AbortController();
let signal = controller.signal;

// 请求与取消请求的按钮及事件
const downloadBtn = document.querySelector(".download");
const abortBtn = document.querySelector(".abort");
downloadBtn.addEventListener("click", fetchVideo);
abortBtn.addEventListener("click", function () {
  // 调用 AbortController 实例的 abort 方法来取消所有使用该信号的 fetch 请求
  controller.abort();
  console.log("Download aborted");
});

function fetchVideo() {
  // 将 AbortController 实例的 signal 传递给 fetch options 的 signal 属性
  fetch(url, { signal })
    .then(function (response) {
      //...
    })
    .catch(function (e) {
      reports.textContent = "Download error: " + e.message;
    });
}
```

## axios 取消请求

axios 取消请求使用了已被撤回的可取消 Promise 提案 [cancelable promises proposal](https://github.com/tc39/proposal-cancelable-promises)，初步的使用参考[axios 官方文档 Cancellation](https://github.com/axios/axios#cancellation)。

axios 官网文档只涉及到了初步的使用，这里还有两点需要注意：

1. 当请求被取消后，就无法再次调用了，需要重置 CancelToken 为新的 CancelToken 实例后才能再次请求；
2. 可以在每个请求中使用不同的 CancelToken 实例，也可以全局使用一个 CancelToken，来批量取消请求；

下面是批量取消请求的示例，这里把全局的 CancelToken 实例存储在 window.globalVar 对象下，用于全局访问

```ts
// 在请求文件中，拦截所有请求，使用全局的CancelToken实例为请求添加cancelToken属性
import axios from "axios";

export const Axios = axios.create({
  baseURL: baseUrl,
  withCredentials: true,
  timeout: 10000,
});
// 请求拦截函数
function handleRequest(req: AxiosRequestConfig) {
  // 请求时增加全局取消token
  req.cancelToken = window.globalVar.cancelTokenSource?.token;
  // ...
}
function handleError(err: AxiosError) {
  return Promise.reject(err);
}
Axios.interceptors.request.use(handleRequest, handleError);

// 在路由文件中，当路由改变需要批量拦截请求时
import axios from "axios";
// 批量取消所有请求
window.globalVar.cancelTokenSource?.cancel("上个请求已取消");
// 重置全局CancelToken实例，防止下次发起请求时，请求仍处于被取消状态
window.globalVar.cancelTokenSource = axios.CancelToken.source();
```

> 参考资料
>
> [单页面应用批量取消请求的最佳实践](https://github.com/chunpu/blog/issues/98)
