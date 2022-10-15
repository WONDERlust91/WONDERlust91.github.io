---
title: windows11打开txt乱码
date: 2022-04-29 11:02:04
tags:
  - txt
  - 乱码
  - word
categories: windows
---

## 问题描述

系统升级 windows11 后，打开 windows10 1903 及以前版本系统建立的 txt 文件，中文部分会出现乱码。网上说是由于 windows10 1903 及以前版本 txt 默认使用 ANSI 编码，而 windows 1903 版本后及 windows11 都默认使用 UTF-8 编码。网上的解方案是改变系统语言，实测并没有生效，且实际需求是保持系统语言不变。

## 解决方案

使用 word 打开 txt 文件，通常会自动弹出编码选择界面，并可预览。这时发现，不论是选择 ANSI 还是 UTF-8，依然还是乱码。选择 GB2312 或 GB18030，此时汉字显示正确，在文件中选择另存为，选好保存地址后，自动弹出保存编码选择界面，改为 UTF-8 保存。使用 notepad 打开，汉字显示正常了。

<!-- more -->

## 其他知识

### 使用 word 打开 txt 文件没有编码选择

如果 word 打开 txt 文件没有弹出编码选择界面，可以在 options-->advanced-->general 下勾选 confirm file format conversion on open，设置完成后退出 word，再次打开 txt，就会有编码选择的界面了。

### 重新保存的文件编码为 UTF-8 with BOM

仔细看会发现，被 word 重新保存后的 txt 文件，在 notepad 中显示的编码是 UTF-8 with BOM，而不是 UTF-8。

### 什么是 BOM

BOM（byte order mark）是为 UTF-16 和 UTF-32 准备的，用于标记字节序（byte order）。

UTF-8 不需要 BOM，尽管 Unicode 标准允许在 UTF-8 中使用 BOM。所以不含 BOM 的 UTF-8 才是标准形式，在 UTF-8 文件中放置 BOM 主要是微软的习惯（顺便提一下：把带有 BOM 的小端序 UTF-16 称作「Unicode」而又不详细说明，这也是微软的习惯）。微软在 UTF-8 中使用 BOM 是因为这样可以把 UTF-8 和 ASCII 等编码明确区分开，**但这样的文件在 Windows 之外的操作系统里会带来问题**。「UTF-8」和「带 BOM 的 UTF-8」的区别就是有没有 BOM。即文件开头有没有 U+FEFF。UTF-8 的网页代码不应使用 BOM，否则常常会出错。

### 保存为 UTF-8（而非 UTF-8 with BOM）

可见如果为了在非微软系统中有好的兼容性，txt 文件尽量还是使用标准的 UTF-8 编码。可以在 windows11 系统中新建 txt 文档，此时文档默认编码为 UTF-8，再从 word 中将已改变编码正常显示的文字复制到新的文档中，保存即可。

> 参考资料
>
> [「带 BOM 的 UTF-8」和「无 BOM 的 UTF-8」有什么区别？网页代码一般使用哪个？](https://www.zhihu.com/question/20167122)
>
> [Choose text encoding when you open and save files](https://support.microsoft.com/en-us/office/choose-text-encoding-when-you-open-and-save-files-60d59c21-88b5-4006-831c-d536d42fd861?ns=winword&version=16&ui=en-us&rs=en-us&ad=us)
