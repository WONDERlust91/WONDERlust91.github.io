---
title: react在组件外使用redux的dispatch
date: 2022-01-13 11:31:25
tags:
  - redux-toolkit
categories: react
---

## 应用场景

使用 redux-toolkit 管理全局状态（如请求的 token），将一些异步操作也写在 redux 中（如登录接口）。使用 axios 请求数据，在 axios 拦截器中又需要操作全局状态（如获取请求 token、请求发现登录过期，自动退出登录）。这就意味着要在 react-component 之外进行 store 的 state 获取、或者 action 的 dispatch 操作，而在非 tsx 文件中，无法使用 useDispatch 的 hook。

<!-- more -->

全局入口文件

```ts
import React from "react";
import ReactDOM from "react-dom";

import { Provider } from "react-redux";
// 引入生成的 store
import store from "./store/index";

import App from "./App";
import reportWebVitals from "./reportWebVitals";

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById("root")
);

reportWebVitals();
```

App 文件，执行登录逻辑

```ts
import { useEffect } from "react";
import { useDispatch } from "react-redux";
import { loginAsync } from "./store/auth/slice";

function App() {
  const dispatch = useDispatch();
  useEffect(() => {
    dispatch(loginAsync());
  }, []);
  return <div></div>;
}
```

redux store 文件

```ts
/**
 * store/index.ts
 */
import rootReducers from "./reducers";
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({ reducer: rootReducers });

export default store;
```

root reducers 文件

```ts
/**
 *  store/reducers.ts
 */
import authReducer from "./auth/slice";
import { combineReducers } from "@reduxjs/toolkit";

const rootReducers = combineReducers({
  authReducer,
});

export default rootReducers;
```

某个单独的 slice 文件

```ts
/**
 * store/auth/slice.ts
 */
import { createSlice } from "@reduxjs/toolkit";
// 注意这里有请求的引入
import { loginApi } from "../../api/auth";

const authSlice = createSlice({
  name: "auth",
  initialState: {
    token: "",
  },
  reducers: {
    setToken(state, action) {
      state.token = action.payload;
    },
    logout(state, action) {
      // 退出登录的逻辑
    },
  },
});

// 这里有个异步的 action 用到了请求 loginApi
export const loginAsync = () => async (dispatch) => {
  try {
    const token = await loginApi();
    dispatch(setToken(token));
  } catch (err) {
    console.error(err);
  } finally {
  }
};

export const { setToken, logout } = authSlice.actions;
export default authSlice.reducer;
```

请求文件

```ts
/**
 * api/auth.ts
 */
import axios from "axios";

const Axios = axios.create({
  baseURL: "/baseUrl",
  withCredentials: true,
  timeout: 10000,
});

function handleRequest(req) {
  // 请求拦截
  // 需求是在请求中加入token
}
function handleResponse(res) {
  // 响应拦截
  // 需求是在响应中，执行拦截已过期的请求，并退出登录
}
function handleError(err) {
  return Promise.reject(err);
}

Axios.interceptors.request.use(handleRequest, handleError);
Axios.interceptors.response.use(handleResponse, handleError);

// 具体的请求
export const loginApi = Axios.get("/login");
```

## 网上常见的解决方案

查阅资料，网上最常见的解决方案就是，引入生成的 store，使用 store 中带有的 store.dispatch 方法来操作全局状态。

在请求文件中做如下修改

```ts
import axios from "axios";
import store from "../../store";
import { logout } from "../../store/auth/slice";

const Axios = axios.create({
  baseURL: "/baseUrl",
  withCredentials: true,
  timeout: 10000,
});

function handleRequest(req) {
  // 请求拦截
  req.headers.authorization = store.getState().auth.token;
  return req;
}
function handleResponse(res) {
  // 响应拦截
  store.dispatch(logout());
}
function handleError(err) {
  return Promise.reject(err);
}

Axios.interceptors.request.use(handleRequest, handleError);
Axios.interceptors.response.use(handleResponse, handleError);

// 具体的请求
export const loginApi = Axios.get("/login");
```

这样看起来是解决了问题，但是当无意中在全局入口文件里改变了 App 与 store 的引入顺序，程序报错了，提示 Store does not have a valid reducer。

全局入口文件

```ts
import React from "react";
import ReactDOM from "react-dom";

import { Provider } from "react-redux";

import App from "./App";

// 将 store 放在 App后引入，会得到 Store does not have a valid reducer 的错误
import store from "./store/index";

import reportWebVitals from "./reportWebVitals";

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById("root")
);

reportWebVitals();
```

虽然说把顺序改回去，就不会有报错，但还是需要探究下问题背后的原因。我们都知道正常情况下，import 的顺序并不会影响代码的执行。为什么这里改了下顺序就出现了错误呢。

参考[redux-toolkit/issues/842](https://github.com/reduxjs/redux-toolkit/issues/842)，这个 issue 指出造成这个错误的原因是循环引用（circular dependency）。store 中引用了 rootReducers，rootReducers 中引用了 authSlice，authSlice 中的 loginAsync 异步操作中的请求引自 axios 实例，axios 实例中又引用 store。若是 store 先加载，App 后加载，App 中的登录页面用到了 loginAsync 异步操作，此时 store 已经初始化，则不会报错。若是 App 先加载，App 的登录 页面引用了 loginAsync 异步操作，此时 store 尚未引入并初始化，就会报错了。

## 最终解决方案

参考 Redux 官方的 FAQ [How can I use the Redux store in non-component files?](https://redux.js.org/faq/code-structure#how-can-i-use-the-redux-store-in-non-component-files)一个应用中只应该有一个 redux store，在 react 中，通过运行时渲染顶层的 `<Provider store={store}><App /></Provider>` 来将 store 注入 `<App />` 应用中，因此**只有**在这个顶层的入口文件中可以使用 import 直接引入 store。

当然有时候也会需要在其他地方使用 store 进行交互。但非常重要的是，**要避免在除入口文件外的其他地方直接 import store**，虽然这在有时候是能生效的，但更多的时候则会导致循环引入依赖的错误。

这里有几种可能的解决方案：

- 将依赖 store 的逻辑写成一个 thunk，然后通过在组件中调用 thunk 来解决;

- 从组件中传出 dispatch 作为一个方法的参数，以便调用;

- 将依赖 store 的逻辑写成一个自定义的中间件，然后在 store 初始化的时候加入中间件;

- 当顶层 App 中的 store 创建后，将之注入要用到 store 的文件中;

一个常见的案例在 axios 的拦截器中读取 redux store 中存储的 API 授权状态，例如 token。axios 拦截器需要引用 `store.getState()` 方法，也需要被引入到其他 API 层的文件中，这就会导致循环引入的问题。

那么就可以在 axios 拦截器的文件中暴露一个`injectStore`的方法：

```ts
let store;
export const injectStore = (_store) => {
  store = _store;
};

axiosInstance.interceptors.request.use((req) => {
  req.headers.authorization = store.getState().auth.token;
  return req;
});
```

之后在顶层入口文件中，将 store 注入 axios 拦截器文件

```ts
import store from "./app/store";
import { injectStore } from "./common/api/";

injectStore(store);
```

这样就实现了顶层入口文件是唯一引入 store 的文件，并避免了循环引入的问题。

> 参考资料
>
> [Store does not have a valid reducer when dispatch is called outside a component](https://github.com/reduxjs/redux-toolkit/issues/842)
>
> [how to dispatch action outside component in Redux-Toolkit + typescript?](https://stackoverflow.com/questions/70122391/how-to-dispatch-action-outside-component-in-redux-toolkit-typescript/)
>
> [Only One Redux Store Per App](https://redux.js.org/style-guide/style-guide#only-one-redux-store-per-app)
>
> [How can I use the Redux store in non-component files?](https://redux.js.org/faq/code-structure#how-can-i-use-the-redux-store-in-non-component-files)
