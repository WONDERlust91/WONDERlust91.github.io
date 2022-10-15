---
title: windows10本地用户密码重置
date: 2021-12-16 17:01:23
tags:
  - windows10
  - 密码重置
  - 忘记密码
categories: windows
---

win10 系统忘记用户密码，无法登录。首先区分帐户类型：

- 已登录 windows 帐号的网络帐户，直接在 windows 的网络帐户管理网站上重置密码即可，参考[重置忘记的 Microsoft 帐户密码](https://support.microsoft.com/zh-cn/account-billing/%E9%87%8D%E7%BD%AE%E5%BF%98%E8%AE%B0%E7%9A%84-microsoft-%E5%B8%90%E6%88%B7%E5%AF%86%E7%A0%81-eff4f067-5042-c1a3-fe72-b04d60556c37);

- 本地帐户如记得密码提示问题，或生成过密码重置盘，则可以直接重置，否则就需要一些额外的技术手段来重置了。

## 重置密码，不使用第三方软件，仅需系统安装盘

### 原理

使 cmd 命令行工具在系统环境下，绕过 windows 密码执行，然后重置用户密码

### 方案

win10 登录界面右下角有一个轻松使用按钮，将轻松使用替换为 cmd 命令行，再重置用户密码

<!-- more -->

### 实施

1. 制作或找一个 windows 安装 U 盘;

2. 从 windows 安装 U 盘启动，启动后在重装系统界面，按`shift + f10`打开命令行;（虽然这里打开了命令行，但并不是在我们使用的系统环境下，仅是预安装环境，故无法读取到我们系统环境下的用户）

3. 输入系统所在盘符，例如系统在 C 盘，输入`c:`，然后回车，进入 c 盘;

4. `cd C:\Windows\System32`，进入 System32 目录;

5. `rename utilman.exe utilman2.exe`，重命名 轻松使用 程序，防止被覆盖;

6. `copy cmd.exe utilman.exe`，复制 命令行 程序 并命名为 轻松使用;

7. 关闭 cmd 窗口，退出预安装环境，重启电脑;

8. 在登录界面点击 轻松使用 此时将弹出 cmd 程序;

9. 在 cmd 程序中输入`net user <忘记密码的用户名> *`，重置密码；也可以输入`control userpasswords2`在弹窗中选中忘记密码的用户，点击重置密码;

10. 退出 cmd 程序，使用重置的密码进入系统，到系统盘的`windows/system32`目录下将 cmd 伪装的`utilman.exe`删除，将`utilman2.exe`重命名回`utilman.exe`;

## 重置密码，使用第三方软件

一些老牌的 pe 系统都会带有重置用户密码的功能，如老毛桃、大白菜等，但体积大，不纯净，还会植入广告等，不好用。

这里推荐一个国外的软件[lazesoft](https://www.lazesoft.com/index.html) password recovery。

下载后安装，然后插入 u 盘，按提示将重置环境烧录到 u 盘中，注意 u 盘会被格式化，然后在要重置密码的电脑上重启到 u 盘，在 u 盘环境中，按提示重置密码，再重新启动即可。
