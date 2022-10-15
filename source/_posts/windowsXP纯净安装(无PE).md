---
title: windowsXP纯净安装（无PE）
date: 2018-06-01 19:36:26
tags:
categories: windows
---

## winXP 无法直接用 u 盘直接安装

win7 及以后的系统都可以直接用 ultraiso 在 U 盘中写入微软原版硬盘映像，然后引导安装微软纯净系统，但 winXP 用这个办法安装却会报错，无法进入安装环境。因此 winXP 时代重装系统多是利用第三方 PE（预安装环境）和 ghost，这虽然方便但是会发生第三方篡改系统的问题。这里我就说说如何用 win7 及以上安装 U 盘来装 winXP。

原理：利用 win7 的预安装环境来运行 winXP 安装文件

准备材料：2 个 U 盘，ultraiso，winXP.iso，win7.iso or win10.iso（用于当启动盘）

<!-- more -->

1. 将 win7.iso 用 ultraiso 写入硬盘映像装入 U 盘 1；
2. 将 winXP.iso 解压入 U 盘 2；
3. 将 U 盘 1 与 U 盘 2 接到目标计算机，从 U 盘 1 启动，进入 win7 安装环境；
4. 使用 win7 安装环境做好硬盘分区和格式化（也可进入 diskpart 后用 diskpart 在命令行格式化）；
5. 按下 shift+F10 打开命令行，输入 diskpart 回车，进入 diskpart 分区工具；
6. 输入 list volume，回车查看 U 盘 2 的盘符与安装 winXP 的目标盘符，例如 U 盘 1 是 X 盘，U 盘 2 是 F 盘，目标硬盘是 C 盘；（本来原理上是可以在命令行中直接运行 F 盘里 xp 的 SETUP.EXE，但是由于 win7 预安装环境是比 XP 更先进的系统，兼容性检查不允许安装低版本系统，直接运行 SETUP.EXE 会发现安装选项是灰色，因此要按下面步骤操作）
7. 输入 exit 退出 diskpart，回到 cmd，输入 F：进入 F 盘；
8. 复制 U 盘 2 中的所有文件到硬盘的另一个分区 D，输入 xcopy .\ D:\ /s /e （.\代表当前工作目录，D:\代表目标目录，/s 参数代表复制所有目录和文件夹，/e 参数代表复制包括空文件夹），若不执行此步，有的电脑安装时会出现 asms.01.cab 找不到的问题；
9. 输入 cd i386(64 位系统为 cd amd64)，进入 i386（amd64）文件夹，输入 winnt32.exe /syspart:C /tempdrive:C /makelocalsource（syspart 参数指定安装的系统盘，tempdrive 参数指定安装文件临时盘，如不指定会安装到当前的 U 盘 1 即 X 盘，导致无法安装，makelocalsource 将 winXP 安装包拷备到计算机，以后安装功能不用再装入安装光盘）；
10. 这时会跳出 xp 安装界面，复制安装文件，复制完后就自动退出了；
11. 退出 win7 安装环境，重启电脑从硬盘启动就开始安装 winXP 了。

如果看到这你成功了，说明你还是挺厉害的。但是**前方高能预警**

---

前面的教程都是 bull shit，主要是 ultraiso 对 xp 支持不好，导致不能进入安装环境。我们换一款名为[rufus](https://rufus.akeo.ie/)软件再来将映像写入 U 盘，发现一切就像直接用 win7 映像安装系统一样简单。（rufus 不支持 win xp 64 位）
