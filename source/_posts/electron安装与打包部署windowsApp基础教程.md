---
title: electron安装与打包部署windowsApp基础教程
date: 2019-06-03 14:59:11
tags:
  - windows
  - app
categories: electron
---

## 为 electron 项目新建一个目录，并初始化 npm

例如新建文件目录为 testDesktopApp，再初始化 npm

```shell
npm init
# 一路回车即可
```

修改 package.json 文件，将 name 修改为最终桌面可执行程序的名称，将 main 中的 index.js 修改为 main.js 防止与要打包项目的 js 文件冲突，并在 script 中添加 start 命令。

```json
{
  "name": "testdesktopapp",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "electron ."
  },
  "author": "",
  "license": "ISC"
}
```

<!-- more -->

## 在项目中安装 electron

使用--registry 与--ELECTRON_MIRROR 命令从淘宝源安装 electron

```shell
npm install --save-dev electron --registry=https://registry.npm.taobao.org --ELECTRON_MIRROR=https://npm.taobao.org/mirrors/electron/
```

新建 main.js 文件

```javascript
const { app, BrowserWindow, Menu, Tray } = require("electron");
function createWindow() {
  // 不显示菜单栏
  Menu.setApplicationMenu(null);
  // 创建浏览器窗口
  let win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  });

  // 加载index.html文件
  win.loadFile("./www/index.html");

  // // 打开开发者工具
  // win.webContents.openDevTools();

  // 当 window 被关闭，这个事件会被触发。
  win.on("closed", () => {
    // 取消引用 window 对象，如果你的应用支持多窗口的话，
    // 通常会把多个 window 对象存放在一个数组里面，
    // 与此同时，你应该删除相应的元素。
    win = null;
  });
}

app.on("ready", createWindow);
// 当全部窗口关闭时退出。
app.on("window-all-closed", () => {
  // 在 macOS 上，除非用户用 Cmd + Q 确定地退出，
  // 否则绝大部分应用及其菜单栏会保持激活。
  if (process.platform !== "darwin") {
    app.quit();
  }
});

app.on("activate", () => {
  // 在macOS上，当单击dock图标并且没有其他窗口打开时，
  // 通常在应用程序中重新创建一个窗口。
  if (win === null) {
    createWindow();
  }
});
```

## 前端文件的准备

1. 如果是原生的 js 文件，没有使用框架，可以直接放入 electron 项目的 www 目录中，如 testDesktopApp 下的 www 文件夹
1. 如果使用了 vue 框架，则需要修改 router.js 中 mode 为 hash，vue.config.js 中 publicPath 为"./"，再进行打包，将打包后 dist 文件夹中的文件放入 www 文件目录中

## 运行 electron

执行 npm start 命令，即可运行 electron

打包部署 electron 项目使用第三方打包工具 electron-builder

## 安装 electron-builder

```shell
npm install --save-dev electron-builder --registry=https://registry.npm.taobao.org/
```

## 在 package.json 中加入命令

scripts 中增加 pack 与 dist 命令，pack 命令打包出免安装版文件，dist 命令打包出可执行安装文件，不论是安装版还是免安装版，文件都在 dist 目录下。

新增 build 属性，配置打包出 windows 系统下 x86 和 x64 都兼容的文件。

```json
{
  "name": "testdesktopapp",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "electron .",
    "pack": "electron-builder --dir",
    "dist": "electron-builder"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "electron": "^5.0.2",
    "electron-builder": "^20.41.0"
  },
  "build": {
    "win": {
      "target": [
        {
          "target": "nsis",
          "arch": ["x64", "ia32"]
        }
      ]
    }
  }
}
```

## 修改应用程序名称与图标

应用程序名称默认为 package.json 中的 name 属性，进入应用程序后则会读取 www 下 index.html 的 title 标签。

项目图标直接在 electron 项目目录下放置一个名为 icon 的图标文件即可，要求是 ico 格式或 png 格式，最好是 512\*512 像素的方形图标。

## 部署应用程序，生成安装可执行文件

```shell
npm run dist
```

打包部署完成后会在 electron 项目根目录下生成一个 dist 文件夹，其中 x86 版免安装文件夹为 win-ia32-unpacked，x64 版免安装文件夹为 win-unpacked，安装可执行文件为项目名 + Setup  + 版本号.exe，例如 testdesktopapp Setup 1.0.0.exe。
