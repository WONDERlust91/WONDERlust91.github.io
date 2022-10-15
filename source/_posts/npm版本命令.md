---
title: npm版本命令
date: 2021-04-10 18:23:37
tags: npm
categories: npm
---

## 几种 version 相关的 npm 命令

```bash
# 通过命令变更package.json中的版本号
npm version [<newversion> | major | minor | patch | premajor | preminor | prepatch | prerelease | from-git]
# 查看当前npm版本
npm [-v | --version]
# 查看某个已发布包的版本号
npm view <pkg> version
# 查看当前目录下所有包与依赖的版本
npm ls #还可以使用--depth=<number>参数来选择查看的目录层级
```

<!-- more -->

## npm version 相关命令配置

### allow-same-version

- default: false
- type: boolean

允许 npm version 将新的版本设置为与当前版本相同值，默认是不允许

### git-tag-version

- default: true
- type: boolean

当使用 git version 命令时 git 自动提交一个最新版本的 tag，默认提交

### commit-hooks

- default: true
- type: boolean

当使用 git version 命令时调用 git 提交钩子，默认为调用

### sign-git-tag

- default: false
- type: boolean

将 -s 标志传递给 git 进行 tag 签名，默认关闭；注意必需在 git config 中配置 GPG key 才能正确使用该配置

### workspaces

- default: false
- type: boolean

启用工作区环境，当设置新的版本时，仅有工作区内的会变更，默认不开启

### workspace

- default: []
- type: array

开启工作区并限定该配置指定的文件或文件夹为工作区

## 描述

在包目录下运行 npm version 命令来升级版本，新的版本号将写入 package.json，package-lock.json，npm-shrinkwrap.json（若存在）。

`newversion` 参数必需是一个 semver 字符串(semver 为 semantic-version 的缩写，详见[node-semver 库的说明](https://github.com/npm/node-semver))或者 semver 参数（`patch`, `minor`, `major`, `prepatch`, `preminor`, `premajor`, `prerelease`)或`from-git`。在使用 semver 参数的情况下，指定的版本号字段将自增 1。使用`from-git`参数会读取最新的 git tag 作为 npm 版本号。

若是在一个 git 版本库中执行 npm version 命令，这将自动创建一次 git commit 与 tag。该行为受到 git-tag-version 配置的控制，也可以被命令禁用`npm --no-git-tag-version version`。另外要注意的是当工作区不是 clean 状态时，npm version 命令会失败，除非使用 `-f` 或 `--force` 参数。

如果使用 `-m` 或 `--message` 参数，将会使用后面的字符串作为提交信息，如果提交信息中包含`%s`字符串，它将会被版本号替换。

`npm version patch -m "Upgrade to %s for reasons"`

如果配置 sing-git-tag 为 true，相当于向 git tag 传递了-s 参数，进行签名，这需要你在 git config 中提供 GPG key。

```bash
$ npm config set sign-git-tag true
$ npm version patch
You need a passphrase to unlock the secret key for
user: "isaacs (http://blog.izs.me/) <i@izs.me>"
2048-bit RSA key, ID 6C481CF6, created 2010-08-31
Enter passphrase:
```
