---
title: windows查看激活状态
date: 2018-06-01 21:10:01
tags:
categories: windows
---

## winXP 查看激活状态

win+R 打开运行对话框，输入 oobe/msoobe /a 查看是否激活

## win7 及以上系统查看激活状态

win+R 打开运行对话框，输入

slmgr.vbs -dlv 查看完整授权状态，

slmgr.vbs -dli 查看简化授权状态，

slmgr.vbs -xpr 查看是否永久激活。

<!-- more -->

## office 查看激活状态与管理密钥

win+R 运行 cmd，键入 cd C:\Program Files\Microsoft Office\Office1X 进入 office 目录（不同版本 Office，X 数字不同，安装位置可能也不同）。

输入 cscript ospp.vbs /dstatus 查看密钥详细信息，

cscript ospp.vbs /unpkey:XXXXX 删除密钥（XXXXX 为详细信息中查到的密钥后五位），

添加密钥 cscript ospp.vbs /inpkey:XXXXX-XXXXX-XXXXX-XXXXX-XXXXX。

还有其他 ospp.vbs 命令可自行上网查找。
