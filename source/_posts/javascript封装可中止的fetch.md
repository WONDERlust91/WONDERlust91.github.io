---
title: javascript封装可中止的fetch
date: 2022-01-14 11:31:21
tags:
  - fetch
  - abortController
  - 中止请求
  - javascript
categories: javascript
---

## 封装 fetch

```ts
// 请求配置类型，后续可扩展
interface RequestConfig {
  // 基础请求地址
  baseUrl?: string;
  // 超时时间
  timeout?: number;
}

// 请求返回值类型
// 在Promise基础上增加 abortController 属性，可用于手动终止请求
export interface RequestReturn extends Promise<Response> {
  abortController: AbortController;
}

// 重新声明请求类型，在 fetch 默认的 input、init 属性基础上增加了 config 属性，返回值在 Promise 基础上增加了 abortController 属性
interface Request {
  (
    input: RequestInfo,
    init?: RequestInit,
    config?: RequestConfig
  ): RequestReturn;
  create?: any;
}

// 直接调用，该形式类似直接使用 axios
const request: Request = (input, init?, config?) => {
  const controller = new AbortController();
  const signal = controller.signal;
  setTimeout(
    () => {
      controller.abort();
    },
    // 默认10秒超时
    config?.timeout !== null && config?.timeout !== undefined
      ? config?.timeout
      : 10000
  );
  // 由于js只能声明函数，而不能直接声明可调用对象，故使用 Object.assign 的方式将属性与函数整合在一起
  return Object.assign(
    fetch((config?.baseUrl || "") + input, { signal, ...init }),
    { abortController: controller }
  );
};

// 创建实例，该形式类似使用 axios.create 创建实例
request.create = (config: RequestConfig) => {
  const request: Request = (input, init?, instanceConfig?) => {
    const controller = new AbortController();
    const signal = controller.signal;
    setTimeout(() => {
      controller.abort();
    }, instanceConfig?.timeout || config.timeout || 10000);
    return Object.assign(
      fetch((instanceConfig?.baseUrl || config.baseUrl || "") + input, {
        signal,
        ...init,
      }),
      { abortController: controller }
    );
  };
  return request;
};

export default request;
```

<!-- more -->

## 使用封装后的 fetch

```ts
import request from "./api";

/**
 * 直接使用，声明请求
 */
const getXXXData = request(
  "/baseUrl/xxx",
  {
    method: "POST",
    body: {
      param1: "param1",
      param2: "param2",
    },
    headers: new Headers({
      "Content-Type": "application/json",
    }),
  },
  {
    timeout: 5000,
  }
);

/**
 * 创建实例，再声明请求
 */
const xxxRequest = request.create({
  baseUrl: "/baseUrl",
  timeout: 5000,
});
const getXXXData = xxxRequest("/xxx", {
  method: "POST",
  body: {
    param1: "param1",
    param2: "param2",
  },
  headers: new Headers({
    "Content-Type": "application/json",
  }),
});

// 某些条件下，手动终止请求（如页面切换，但请求还未返回）
getXXXData.abortController.abort();

// 获取请求结果
getXXXData
  .then((res) => {
    return res.json();
  })
  .then((res) => {
    console.log(res);
  })
  .error(console.error);
```

> 参考资料
>
> [Abortable fetch](https://developers.google.com/web/updates/2017/09/abortable-fetch)
