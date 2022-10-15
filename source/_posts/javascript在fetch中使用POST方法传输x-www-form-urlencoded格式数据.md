---
title: javascript在fetch中使用POST方法传输x-www-form-urlencoded格式数据
date: 2021-05-06 10:00:07
tags:
  - javascript
  - fetch
  - x-www-form-urlencoded
  - URLSearchParams
categories: javascript
---

## 使用 fetch

fetch 与 XMLHttpRequest 实现的 jQuery.ajax() 有以下三点不同：

- 当接收到一个代表错误的 HTTP 状态码时（即使响应的 HTTP 状态码是 404 或 500），从 fetch 返回的 Promise 不会被标记为 reject。它会将 Promise 状态标记为 resolve（但是会将 resolve 返回值的 ok 属性设置为 false），仅当网络故障时或请求被阻止时，才会标记为 reject；
- fetch 可以接受跨域 cookies；
- fetch 默认不发送 cookies，除非你使用了 credentials 选项；

```javascript
fetch("http:// example.com/movies.json").then(function (response) {
  return response.json();
});
```

`fetch()` 获取到的只是一个 HTTP 响应，而不是真正的 JSON，为了获取真正的 JSON 内容，需要使用 `json()` 方法。

<!-- more -->

### fetch 语法与支持的请求参数

`Promise<Response> fetch(input[, init]);`

#### input 定义了要获取的资源

- 可以是一个[USVString](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString)字符串，包含要获取资源的 URL；
- 也可以是一个 Request 对象

#### init 可选，一个配置对象，包括所有对请求的设置

- method: 请求使用的方法，如 GET、POST；
- headers: 请求的头信息，形式为[Headers](https://developer.mozilla.org/zh-CN/docs/Web/API/Headers)的对象或包含[ByteString](https://developer.mozilla.org/zh-CN/docs/Web/API/ByteString)值的对象字面量；
- body: 请求的 body 信息，可能是[Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)、[BufferSource](https://developer.mozilla.org/en-US/docs/Web/API/BufferSource)、[FormData](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)、[URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)或[USVString](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString)对象。注意 GET 或 HEAD 方法的请求不能包含 body 信息；
- mode: 请求的模式，如 cors、no-cors 或 same-origin；
- credentials: 自动发送 cookie 的选项，不提供不自动发送，可选值为 omit（不发送）、smae-origin（仅同源时发送）、include（不论同源或跨域，均发送）；
- cache：请求的缓存模式，可选值为 default、no-store、reload、no-cache、force-cache、only-if-cached；
- redirect: 重定向模式，可选值为 follow（自动重定向）、error（如果产生重定向将自动终止并且抛出一个错误）、manual（手动处理重定向），Chrome 中默认为 follow；
- referrer: 引荐来源，该字段告诉服务器，用户在访问当前资源之前的位置，可用来引流分析与用户跟踪，可选值为 client、no-referrer、或 URL，默认为 client；
- referrerPolicy: 指定了 HTTP 头部 referer（拼写无误，是 HTTP 标准建立时，就拼写错误，承袭至今），可选值为 no-referrer、no-referrer-when-downgrade、origin、origin-when-cross-origin、unsafe-url；
- integrity: 包括请求的[subresource integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)值（ 例如： sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=）；

```javascript
fetch("http://example.com/answer", {
  body: JSON.stringify({ answer: 42 }), // must match 'Content-Type' header
  cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
  credentials: "same-origin", // include, same-origin, *omit
  headers: {
    "user-agent": "Mozilla/4.0 MDN Example",
    "content-type": "application/json",
  },
  method: "POST", // *GET, POST, PUT, DELETE, etc.
  mode: "cors", // no-cors, cors, *same-origin
  redirect: "follow", // manual, *follow, error
  referrer: "no-referrer", // *client, no-referrer
}).then((response) => response.json());
```

### Response 对象

Response 实例是在 fetch 处理完 promise 之后返回的。常见的 response 属性有

- Response.status 响应状态码，默认值为 200；
- Response.statusText 响应状态文本（默认为"OK"），与状态码相对应；
- Response.ok 该属性用来检查 response 的状态是否在 200-299 这个范围内，该属性值类型为布尔值；

### Body

不论请求或响应都能够包含 body 对象。body 也可以是以下任意类型的实例。

- [ArrayBuffer](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
- [ArrayBufferView](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)（Unit8Array 等）
- [Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)/File
- string
- [URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)
- [FormData](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)

Body 类定义了以下方法（这些方法都被 Request 和 Response 对象所实现）以获取 body 内容。这些方法都会返回一个被解析后的 Promise 对象和数据。

- [arrayBuffer()](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/arrayBuffer)
- [blob()](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/blob)
- [json()](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/json)
- [text()](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/text)
- [formData()](https://developer.mozilla.org/zh-CN/docs/Web/API/Body/formData)

相比 XHR，这些方法让非文本化的数据使用起来更加简单，请求体可以由传入 body 参数来进行设置：

```javascript
const form = new FormData(document.getElementById("example-form"));
fetch("/example", {
  method: "POST",
  body: form,
});
```

## fetch 中使用 POST 方法传输 x-www-form-urlencoded 格式数据

x-www-form-urlencoded 格式即经常用在 GET 请求路径中跟在?后面的查询字符串 queryString，当我们需要在 post 请求中使用这种格式时（放在 body 中），需要用到 URLSearchParams 类，或通过 npm 安装 qs 库来使用。

### URLSearchParams

该接口定义了一些实用的方法来处理 URL 的查询字符串。一个 URLSearchParams 实例对象可以直接用在 for...of 结构中，如下面两行代码是相等的：

```javascript
for (const [key, value] of mySearchParams) {
}
for (const [key, value] of mySearchParams.entries()) {
}
```

#### 构造函数

URLSearchParams(init)，init 可不传，后续使用 append 方法新增；init 也可传入一个 queryString 或 二维数组键值对 或 对象，返回值为 URLSearchParams 实例。

注意：**URLSearchParams 构造函数不会解析完整 URL，但是如果字符串起始位置有 ? 的话会被去除**

```javascript
const mySearchParams = new URLSearchParams();
const mySearchParams1 = new URLSearchParams("?foo=1&bar=2");
const mySearchParams2 = new URLSearchParams([
  ["foo", 1],
  ["bar", 2],
]);
const mySearchParams3 = new URLSearchParams({ foo: 1, bar: 2 });
```

#### 方法

- [URLSearchParams.append()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/append) 插入一个指定的键/值对作为新的搜索参数
- [URLSearchParams.delete()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/delete) 从搜索参数列表里删除指定的搜索参数及其对应的值
- [URLSearchParams.entries()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/entries) 返回一个 iterator 可以遍历所有键/值对的对象
- [URLSearchParams.get()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/get) 获取指定搜索参数的第一个值
- [URLSearchParams.getAll()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/getAll) 获取指定搜索参数的所有值，返回是一个数组
- [URLSearchParams.has()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/has) 返回 Boolean 判断是否存在此搜索参数
- [URLSearchParams.keys()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/keys) 返回 iterator 此对象包含了键/值对的所有键名
- [URLSearchParams.set()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/set) 设置一个搜索参数的新值，假如原来有多个值将删除其他所有的值
- [URLSearchParams.sort()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/sort) 按键名排序
- [URLSearchParams.toString()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/toString) 返回搜索参数组成的字符串，可直接使用在 URL 上
- [URLSearchParams.values()](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/values) 返回 iterator 此对象包含了键/值对的所有值

### 请求示例

```javascript
// 使用URLSearchParams
const fetchBody = new URLSearchParams({ time: [{ time: "2020-12-12 08:00:00" }] });
// 使用qs库
import qs from "qs";
const fetchBody = qs.stringify({ time: [{ time: "2020-12-12 08:00:00" }] });

fetch(`http://example.com/post`, {
  method: "POST",
  // 注意body使用URLSearchParams需要在头部注明Content-Type，编码方式使用UTF-8，可以防止乱码的情况
  headers: {
    "Content-Type": "application/x-www-form-urlencoded;charset=UTF-8",
  },
  body: fetchBody,
}).then((res) => {
  return res.json();
});
```
