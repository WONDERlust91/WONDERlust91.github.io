---
title: cellphone一加7安装LineageOS指南
date: 2024-02-16 00:26:41
tags:
  - 一加7
  - OnePlus 7
  - LineageOS
categories: cellphone
---

## 遇到的问题

按照 LineageOS 官网指南操作，需要先将手机升级至一加 7 最新的官方系统`氧OS12`，然后再使用 fastboot 进行 OEM BootLoader 解锁，但解锁过程中遇到报错：`(remote: 'Device cannot be unlocked for technical reason.')`。

经查阅 reddit，无法正常解锁的问题需要回退版本至`氧OS11`或更早版本，解锁 OEM BootLoader 后再升级回`氧OS12`，升级完成后再按照 LineageOS 官网操作。

<!-- more -->

### 解锁 OEM BootLoader

1. `氧OS12`无法本地安装更新，需要下载在 ApkMirror 上下载[OnePlus System Update 1.0.0-S-DP beta (READ NOTES)](https://www.apkmirror.com/apk/oneplus-ltd/oneplus-system-update/oneplus-system-update-1-0-0-s-dp-release/#downloads)，以使`氧OS12`能够在本地安装降级包。（注意 OnePlus System Update 的 apk 有两个签名版本：`2352`与`5715`，`2352`与系统更新冲突，选择`5715`下载安装）。

2. 安装后即可从手机本地执行降级操作，网上找到适配一加 7 的`氧OS9`降级包`fulldowngrade_wipe_MSM_18857_190505_1527_user.zip`，放入手机`internal storage`根目录下，使用`OnePlus System Update`进行本地降级操作，降级将导致数据清除。

3. 完成降级后，可按照 LineageOS 官网说明进行 OEM BootLoader 解锁，完成解锁后，再通过 Google Play 上的[Oxygen updater](https://play.google.com/store/apps/details?id=com.arjanvlek.oxygenupdater&hl=en_CA&gl=US)更新到最新的`氧OS12`（也可事先在 Oxygen Updater 上使用 Advanced Mode 提前下载好当前手机对应的最新 ROM：7697b07a-0b88-463f-98af-eb5d8e4d8e5e.zip，然后通过`氧OS9`的系统更新，从本地安装）

4. 解锁并升级到最新的`氧OS12`后，即可继续按照 LineageOS 教程刷入系统了。

### 注意事项

如果需要[Google Apps](https://wiki.lineageos.org/gapps/)，切记在刷入系统后，不要重启进入系统，继续使用 adb 工具 sideload 安装[MindTheGapps-14.0.0-arm64-20231025_234300.zip](https://github.com/MindTheGapps/14.0.0-arm64/releases/tag/MindTheGapps-14.0.0-arm64-20231025_234300)，全部安装结束后再重启首次进入系统。

### 用到的工具

1. [adb 与 fastboot 工具](https://wiki.lineageos.org/adb_fastboot_guide)，[platform-tools_r34.0.5-windows.zip](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)

2. USB 驱动程序（大部分 Win10 以上系统无需安装），[usb_driver_r13-windows.zip](https://developer.android.com/studio/run/win-usb)

## 参考资料

> [OnePlus 7 | FAILED (remote: 'Device cannot be unlocked for technical reason.')](https://www.reddit.com/r/LineageOS/comments/15pupx9/oneplus_7_failed_remote_device_cannot_be_unlocked/)
>
> [Local upgrade not possible in OxygenOS 12?](https://community.oneplus.com/thread/1555340)
>
> [LineageOS WiKi OnePlus 7](https://wiki.lineageos.org/devices/guacamoleb/)
