---
title: 微信小程序使用promise封装wx.request
date: 2022-04-29 20:50:05
tags:
  - 微信小程序
  - promise
categories: 微信小程序
---

## 用 promise 封装 wx.request

在使用小程序请求的过程中我们会用到小程序的 wx.request 接口，但 wx.request 还是基于 callback 封装的，es6 出现后大家更习惯于使用 promise 形式的请求，所以这里我们使用 promise 简单封装一下。

封装需要实现的核心功能：

1. 使用 ES6 Promise;
2. 可以配置 baseUrl;
3. 可以拦截请求参数;
4. 可以拦截响应数据;

<!-- more -->

### 封装的请求

```js
// 目录（/utils/api/index.js）

/**
 * 封装的请求函数
 * @param {object} options wx.request配置对象
 * @param {string} config.baseUrl 接口基础地址
 * @param {() => （{header: object; data: (data:any) => any})} config.interceptorRequest 请求拦截器函数，对data的处理函数需要返回处理后的数据
 * @param {(res: {cookie: string[], data: object, errMsg: string, header: object, statusCode: number}) => any} config.interceptorResponse 响应拦截器函数，需要返回处理后的响应数据
 */
module.exports = function api(options, config) {
  return new Promise((resolve, reject) => {
    wx.request({
      ...options,
      header: {
        ...options.header,
        ...(config && config.interceptorRequest
          ? config.interceptorRequest().header
          : {}),
      },
      data:
        config && config.interceptorRequest && config.interceptorRequest().data
          ? interceptorRequest.data(options.data)
          : options.data,
      url: `${config && config.baseUrl ? config.baseUrl : ""}${options.url}`,
      success: (res) => {
        const resData =
          config && config.interceptorResponse
            ? config.interceptorResponse(res)
            : res;
        resolve(resData);
      },
      fail: (err) => {
        reject(err);
      },
    });
  });
};
```

### 新建请求实例

```js
// 目录（/api/index.js）

const api = require("/utils/request/index");

// 接口基础地址
const fooUrl = "https://your-backend-service.com/foo/";
const barUrl = "https://your-another-backend-service.com/bar/";

// 请求拦截器
const interceptorRequest = () => {
  return {
    header: {
      // 从存储中拿取cookie，假设登录时已存入
      cookie: wx.getStorageSync("cookie") || "",
      // 其他自定义头
      enterprise: "my-enterprise",
    },
  };
};

// 响应拦截器
const interceptorResponse = (res) => {
  console.log(res);
  // 若服务器响应为200，且后端服务响应也为200或后端服务响应不存在code（文件请求的响应为二进制流，无code码），视为正常请求，直接返回请求
  if (res.statusCode === 200 && (!res.data.code || res.data.code === 200)) {
    return res;
  }
  // 若服务器响应为200，且后端服务响应为401，说明用户未登录或登录过期，弹窗提示并reject返回值，在2秒后清空存储中的用户数据，退出登录并跳转至登录页
  else if (res.statusCode === 200 && res.data.code === 401) {
    wx.showToast({
      title: res.data && res.data.msg ? res.data.msg : "登录过期",
      icon: "none",
    });
    setTimeout(() => {
      wx.clearStorage();
      wx.reLaunch({
        url: "/pages/login/login",
      });
    }, 2000);
    return Promise.reject(res);
  }
  // 其他情况皆视为请求出错，弹窗提示并reject返回值
  else {
    wx.showToast({
      title: res.data && res.data.msg ? res.data.msg : "请求失败",
      icon: "none",
    });
    return Promise.reject(res);
  }
};

// 不同后端服务对应的不同请求实例
const apiFoo = (options) =>
  api(options, { baseUrl: fooUrl, interceptorRequest, interceptorResponse });
const apiBar = (options) =>
  api(options, { baseUrl: barUrl, interceptorRequest, interceptorResponse });

module.exports = { apiFoo, apiBar };
```

### 创建接口

```js
// 目录（/api/foo/index.js）

const { apiFoo } = require("/api/index");

const getFooData = () =>
  apiFoo({
    url: `/foo-data`,
  });

const postFooDataUpdate = (data) =>
  apiFoo({
    url: `/foo-data/update`,
    data,
    method: "POST",
  });

const getFooDataDelete = (data) =>
  apiFoo({
    url: `/foo-data/delete/${data.id}`,
  });

module.exports = {
  getFooData,
  postFooDataUpdate,
  getFooDataDelete,
};
```

### 使用接口

```js
// 目录（/pages/foo/foo.js）

const { getFooData, getFooDataDelete } = require("/api/foo/index");

Page({
  // async await式 用法
  async tapDelete(event) {
    wx.showLoading();
    try {
      const deleteId = await getFooDataDelete({ id: event.detail.id });
      console.log("deleted foo data id", deleteId);
      // 执行其他代码逻辑
    } catch (err) {
      console.error(err);
    } finally {
      wx.hideLoading();
    }
  },
  // promise式 用法
  onLoad() {
    getFooData()
      .then((res) => {
        console.log("foo data response", res);
        // 执行其他代码逻辑
      })
      .catch(console.err);
  },
});
```
