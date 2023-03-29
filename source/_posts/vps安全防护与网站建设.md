---
title: vps安全防护与网站建设
date: 2022-05-01 13:41:46
tags:
  - vps
  - 安全
  - 建站
categories: vps
---

本文基于[Project X - 使用指南 - 快速入门文档 - 小小白白话文](https://xtls.github.io/document/level-0)改写而来，原文是非常非常细致且新手友好的教学文档，十分十分推荐大家阅读。本文在原文基础上，删减了部分篇章，增加了部分说明，增加了个人推荐的工具等，作为个人笔记存档。

## 原料准备

### 一台 VPS

首先需要购买一台 VPS。

VPS（Virtual Private Server）虚拟专用主机，传统虚拟主机是多人共享 IP，共享服务器资源，通过用户权限进行隔离，互相之间会影响，不是很方便；而 VPS 之间虽然可能还是同一台物理主机，但通过虚拟化技术，做到了用户之间操作系统级别的完全隔离，具有 VPS 的最高权限，独享 IP，独享带宽，各 VPS 间互不影响。

因此购买云服务器时，一定要购买 VPS，而不是共享式的传统虚拟主机。根据个人喜好，选择合适的云服务器商进行购买。

这里以[bandwagonhost](https://bandwagonhost.com/index.php)为例，10G KVM VPS 年费用为 20$（已无货），20G KVM VPS年费用为50$。

购买好 VPS 后，登录 VPS 管理后台，安装 Linux 系统。如果不熟悉 Linux 各发行版，或有 Linux 发行版选择困难，这里推荐选择 Debian 11。使用 Debian 11 足以让你的 VPS 服务器在安全、稳健运行的同时得到足够的优化（如 cloud 专用内核、及时的 bbr 支持等）。

系统安装完成后，在 VPS 管理后台，查看并记录下 VPS 的 IP 地址与 SSH 端口号、Root 账户密码。

<!-- more -->

### 一个域名

准备一个域名。

域名的购买可以通过国内各大云服务商，国内域名需要备案，国外可以通过 godaddy、nameSilo 等域名商购买。通常`.com`顶级域名更贵，但看起来更正规，有一些需要校验域名的服务，`.com`顶域的兼容性更好。因此推荐优先购买`.com`顶域。

以 [nameSilo](https://www.namesilo.com/) 域名服务商为例，不热门且无人购买的`.com`二级域名，每年的费用为 10$。

> 域名说明
>
> 以`www.your-domain.com`为例，`com`为顶级（一级）域名，`your-domain.com` 为二级域名，`www.your-domain.com` 为三级域名。
>
> 我们购买的是二级域名，二级以上的域名无需再购买，在二级域名的基础上配置即可。

域名购买好后，进入 DNS 设置页面：

1. 配置 A 记录为 VPS 的 IP 地址，即`your-domain.com`对应到 VPS 的 IP 地址；
2. 如果要让`www.your-domain.com`的三级域名也对应到 VPS 的 IP 地址，则再增加一个 CNAME 记录，配置`www`的主机名到`your-domain.com`的二级域名地址；
3. 如果要让多个子域名都对应到 VPS 的 IP 地址（例如`demo.your-domain.com`和`blog.your-domain.com`），可以每个三级子域单独配置 CNAME 记录（同 2），也可以使用通配符`*`作为主机名，然后 CNAME 解析到`your-domain.com`的二级域名地址；

> A 记录与 CNAME 记录
>
> A 记录直接指向 IP 地址，而 CNAME 记录指向域名；
>
> 推荐只在二级域名上使用 A 记录，二级以上的子域名都使用 CNAME 记录，这样在服务器 IP 改变时，只要维护 1 条 A 记录即可，CNAME 记录只要域名不变更，都不需要改变；

### 一些软件

SSH 远程登录工具：

- Windows: [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)（免费、轻量、极简）、[XShell](https://www.xshell.com/en/xshell/)（免费、强大）
- macOS/Linux: Terminal

远程文件拷贝工具：

- Windows：[WinSCP](https://winscp.net/eng/index.php)（免费、轻量）、[XFTP](https://www.xshell.com/en/xftp/)（免费、强大）
- macOS/Linux：Terminal

轻度使用，PuTTY + WinSCP 的组合完全够用，UI 界面可能不够美观；XSHELL 与 XFTP 为同一家公司出品，功能更强，也能协同使用，是很多程序猿的第一选择。

Windows 平台下，个人推荐远程登录与远程文件拷贝结合的工具：[MobaXterm](https://mobaxterm.mobatek.net/)

文本编辑器：

Windows/macOs/Linux：[VSCode](https://code.visualstudio.com/)

## 远程登录

虽然最新版本的 Windows 10 及 Windows 11 都支持使用 PowerShell 和 WSL 来进行 SSH 连接，但并非所有版本都支持。故这里还是推荐大家通过 MobaXterm 软件来进行 SSH 连接。

在 MobaXterm 中，新建一个会话（session），填入 VPS 的 IP 地址和 SSH 端口号，用户名可填写，也可不填，若不填用户名，每次登录过程中会要求填写。

新建成功后，双击会话远程连接 VPS，第一次需要输入用户密码，可选保存或不保存，不保存则每次登录都要输入密码。

连接成功后，就进入了 VPS 的命令行界面（Command Line Interface）。通常 VPS 上的 Linux 系统因为受限于硬盘与内存资源，都不会安装可视化的界面，仅有命令行界面。

连接成功后，命令行界面会默认输出一些信息，如系统版本，内核版本，上次登录时间等，不同 Linux 发行版间可能会稍有不同。

命令行界面的最后一行，闪动的光标左侧，有一串字符，如`root@vps-server:~#`，其中：

- `root`指当前登录的用户；
- `@`后表示用户所在的服务器为`vps-server`；
- `:`后表示用户所在的文件夹为`~`（`~`在 Linux 系统中表示当前用户文件夹）；
- `#`后是用户可以输入命令的地方（注意从网页中复制命令时需要从`#`后开始复制，不要复制命令提示符）；

## 系统更新

与我们熟悉的手机 APP 下载更新逻辑相似，Linux 下，每个 APP 都叫做一个“包” （package）。管理 APP 的程序自然就叫做“包管理器”（Package Manager）。你可以通过它安装、更新、卸载各种软件、甚至更新 Linux 系统本身。Linux 下的包管理器非常强大，此处按下不表，现在只需要知道 Debian 系统的包管理器叫做 apt 即可。

通常安装完 Linux 系统，第一次 SSH 登录后，都需要进行一次全面的包更新。

- 查询更新，获取更新信息

```shell
apt update
```

- 依据获取到的更新信息，执行更新

```shell
apt upgrade
```

执行更新时，会询问是否安装（Y/n），输入 Y，点击回车继续

更新完成后，你的系统已经达到了最佳状态，可以开始使用了。

## 安全防护

### 为什么要做安全防护

Linux 服务器的安全防护是一个纷繁复杂的巨大课题。无数的网站、APP、服务、甚至线下基础设施都建立在 Linux 的基石之上，这背后牵涉到巨大的经济利益和商业价值，当然也就就意味着黑灰产有巨大的攻击动力。但是这些服务是如此重要、根本不允许出现重大的安全漏洞。于是无数的运维专业人员都在安全攻防的战场上拼搏努力，这才让大家能享受到基本稳定的现代化数字生活。

现在，你拥有了一台 VPS，并且将会敞开他的数据访问渠道向外提供服务，那就相当于你已经置身于安全攻防战场的第一线、直面所有风险。

### 具体的风险到底是什么

回想一下之前远程登录时的配置，任何人只需要知道【IP 地址】+【端口】+【用户名】+【密码】这四个要素，就能登录你的 VPS 服务器。那么这四要素就是我们要防护的底线。

1. 【IP 地址】：恶意脚本会随机尝试和扫描 IP 段，可以简单认为是公开信息、无法隐藏；
2. 【端口】：如果使用默认端口，那么【端口 = 22】；
3. 【用户名】：如果使用默认用户，那么【用户名 = root】；
4. 【密码】：密码通常由 VPS 后台随机生成，你也可以自行设置。

也就是说如果你的服务器都是默认设置，则四要素中三个都是已知的，那么服务器的安全就全部寄托在一串密码上了。

- 如果你使用了 VPS 管理后台随机生成的密码，它一般包含随机的十几个大小写混杂的字母、数字和符号，相对较安全；
- 如果你为了好记，把密码改成了诸如 123456 这种弱密码，破解你的 VPS 可谓不费吹灰之力；
- 如果你为了好记，把密码改成了比较复杂、但在别的地方使用过的密码，其实也会有风险。黑客手中有密码库，包含数十万、甚至百万曾经泄漏的真实密码。

黑客不需要坐在电脑前一次一次地尝试你的密码，全部的攻击尝试都是恶意脚本自动进行的，它会 24 小时不眠不休的工作。也许每天你酣睡之时，你的服务器都在经受着一轮又一轮的冲击。

一旦密码被成功撞破，意味着你的四要素全部被攻击者掌握，恶意脚本就会快速登录服务器，获取服务器的最高 root 控制权、安装部署它的恶意服务，然后用你的服务来 24 小时做各种非法事项（如挖矿、传播病毒、发送垃圾邮件、做 BT 中继、甚至成为暗网公众节点等等）。如果恶意脚本比较克制，其实可以做到相当的隐蔽。新人一般也不会去观察留意 VPS 的登录记录、进程变化、CPU 占用变化、流量变化等指标，你就很难发现自己被黑了。直到你的 VPS 服务商封禁你的账号、或者收到律师函才后知后觉。

别忘了，你购买 VPS 时使用了自己的真实支付信息，你登录各种网站、社交平台时也会留下你的 IP 地址，这些都与你的身份有直接或间接的关系。所以，一旦这些违法事项发生，它们就不可避免的与你产生了关联。

### 我们要做的安全防护有哪些

基于上述四要素的分析，【IP】地址是我们服务器的门牌号，不可能隐藏起来，那么我们要做的，自然就是对【端口】、【用户名】、【密码】这三要素进行加强，来降低被攻破的风险：

1. 【端口】：将 SSH 远程登录端口修改为【非 22 端口】；
2. 【用户名】：建立【非 root】的新用户，之后禁用 root 用户使用 SSH 远程登录；
3. 【密码】：SSH 启用密钥验证登录、之后禁用密码登录；

记得按顺序来，别把自己锁在门外。

### 将 SSH 远程登录端口修改为非 22 端口

有些 VPS 服务商，默认使用的端口已经是非 22 端口，那么你可以忽略这一步，当然也可以跟着本文改成你钟意的端口。

> nano
>
> nano 是 Linux 上的文本编辑器，和 vim 一样，都是 Linux 系统预装的。这里之所以选择 nano 而不选大名鼎鼎的 vim，则是因为 Linux 系统预装的 vim 是最小安装版，进入 insert 模式后，最小安装版方向键经常无法移动光标，而变成输入字符，需要安装完全版的 vim 才能解决；另一方面则是因为 vim 的操作对 windows 用户而言是有学习成本的，而 nano 将重要的快捷键都显示在屏幕下方了。

> systemctl
>
> systemctl 是 Linux 系统中服务的管理器，不仅仅是 Linux 的系统服务，很多我们安装的包也会在安装时将自己注册成一个可以被 systemctl 管理的服务，方便我们设置开机自启动或启动、重启、关闭服务。
>
> systemctl 有一些常用的操作
>
> systemctl start 某服务（开启某服务）
> systemctl stop 某服务（停止某服务）
> systemctl restart 某服务（重启某服务，通常在修改配置文件后需要重启）
> systemctl reload 某服务（重载某服务）
> systemctl status 某服务（查看某服务的状态）
> systemctl enable 某服务（开机自动启动某服务）
> systemctl disable 某服务（禁用开机自启动某服务）

> SSH 服务配置文件
>
> SSH 是 Linux 系统中已集成的服务，它的配置文件在`/etc/ssh/sshd_config`

有了上述的前置知识，我们远程登录到自己的 VPS，就可以开始修改 SSH 端口了。

- 首先，用 nano 文本编辑器打开 SSH 远程登录服务配置文件：

  ```shell
  nano /etc/ssh/sshd_config
  ```

  打开配置文件后，我们可以看到重要快捷键已列在屏幕下方，`^`表示`Ctrl`键，`M`表示`Meta`键，即 macOS 中的`Command`键，Windows 中的`Win`键，但由于我们是远程连接的，按下`Win`键触发的本地系统的开始菜单，因此大部分终端软件都将`Meta`键映射为`Alt`键。

- 接下来，在打开的文件中找到`Port`这一项，并修改它的端口

  1. 使用`Ctrl+w`进入搜索模式，输入`Port`并回车；
  2. 将`Port`后的数字（默认为 22）改为大于 1024 小于 65535 的整数（若这一行以`#`开始，说明这行被注释掉了，不生效，把`#`删掉即可）；

- 修改完成后，就是保存文件并退出

  你会发现保存不是 Windows 系统常见的`Ctrl+s`，正确的快捷键：保存是`Ctrl+o`再点击`Enter`，退出是`Ctrl+x`。

- 最后，重启 SSH 服务，使变更生效

  ```shell
  systemctl restart ssh
  ```

现在新的端口已经生效，下次使用 MobaXterm 远程登录 VPS 时就要使用你新改的端口了，别忘了修改你的 MobaXterm 配置。

### 建立非 root 的新用户

Linux 系统中的 root 用户，不仅仅是一个管理员账号那么简单。它是整个系统的【根基】、拥有整个系统中至高无上的权力。一旦 root 账号出现安全问题，整个系统都只能任人鱼肉。

> adduser
>
> Linux 基础命令，给系统新增一个用户
> 使用该命令会：1. 添加用户名；2. 创建用户名相同的组名，并将该用户添加入该组；3. 在/home 目录创建与该用户同名目录，并拷贝/etc/skel 目录下的内容到该目录下；4. 提示输入密码及修改用户信息；
> 这个命令创建的账号，可以用于登录系统
>
> 注意，Linux 还有一个命令 useradd，useradd 仅添加用户名与同名用户组，不创建用户目录也不创建密码，也没有登录 shell，故未进行其他必要操作前，是无法用于登录系统的。
>
> adduser 是一个交互式的命令，将添加用户的流程都完成了，会得到一个直接可用的用户；而 useradd 仅是纯添加用户，未进行其他必要操作，还需要手动执行，才是一个完整可用的账号。
>
> 同理可知，deluser 与 userdel 命令的区别

> sudo
>
> sudo 是让普通用户临时获得 root 权限的功能，sudo 是一个包，需要通过 apt 包管理器安装。
>
> visudo 是打开 sudo 权限配置专用编辑器的命令。
>
> 配置格式为 `USER/GROUP HOST=(USER[:GROUP]) [NOPASSWD:] COMMANDS`
>
> USER/GROUP 表示需要被授权的用户或者组，如果是组，则需要以 % 开头；
> HOST 表示允许从哪些主机登录的用户运行 sudo 命令，ALL 表示允许从任何终端、机器访问；
> (USER[:GROUP]) 表示使用 sudo 可切换的用户或者组，组可以不指定，ALL 表示可以切换到系统的所有用户
> NOPASSWD 如果指定，则该用户或组使用 sudo 时不必输入密码
> COMMANDS 表示运行指定的命令，ALL 表示允许执行所有命令

- 我们要做的第一件事就是【新增一个用户并设定登录密码】：

  ```shell
  # 新增一个名为admin的用户名
  adduser admin
  ```

  执行命令后，根据提示操作即可。注意设置密码时是没有显示的，保证两次输入一致即可。用户附加信息可不填，一路回车即可。

- 第二件事是【安装 sudo 功能】：

  ```shell
  apt update && apt install sudo
  ```

  这一行其实是两个命令，`apt update`是获取最新软件版本信息，`apt install sudo`是安装 sudo 功能，`&&`则是把两个命令连起来执行的意思。

- 第三件事，则是【把 admin 用户加入 sudo 名单中，让他有借用 root 权限的能力】：

  ```shell
  visudo
  ```

  输入该命令后，就会使用编辑器打开 sudo 的配置文件，在`User Privilege Specification`下加入一行`admin ALL=(ALL) NOPASSWD: ALL`即可。

  > 注意
  >
  > NOPASSWD 配置是让 admin 用户临时获取 root 权限时，不用额外输入密码。这与一般安全建议相反。之所以这样做是因为很多新人不顾危险坚持使用 root 账号，仅为了不重复输入密码、觉得轻松。“两害相权取其轻”，直接使用 root 的风险远大于使用 sudo 不输密码的风险，故做了上述配置。
  >
  > 如果希望遵循安全习惯，每次使用 sudo 时输入密码，则这行配置改为：`admin ALL=(ALL:ALL) ALL`即可。

### 禁用 root 用户远程登录

打开 ssh 的配置文件

```shell
nano /etc/ssh/sshd_config
```

找到`PermitRootLogin`这一项，将设定值`yes`改为`no`即可：

- `Ctrl+w`进入搜索模式，输入`PermitRootLogin`并回车，删除`yes`改为`no`
- `Ctrl+o`，并按回车保存，`Ctrl+o`退出

最后重启 ssh 服务，让变更生效

```shell
systemctl restart ssh
```

下次登录时 root 用户就无法连接了，记得在 MobaXterm 中将登录用户改为刚刚新增的 admin 用户。

### 使用密钥登录并禁用密码登录

最后，我们来解决密码可能被撞库破解的问题。

前面已经说过，黑客并不是很蠢地使用穷举法破解你的密码，而是用泄漏的密码库来撞库破解，除非你用的是随机长密码（如借助 1Password，或 macOS 的 keychain 等密码管理工具），否则很容易中招。

随机长密码虽然安全性有所提高，但是基本无法记忆，手动输入也很容易错。为了解决这个问题，我们直接弃用【密码验证】方式，改用【密钥验证】。

密钥验证，就是生成一对相关联的密钥文件（公钥和私钥），然后把公钥上传到 VPS。每次登录时，SSH 会将公钥和私钥进行匹配，若验证是正确的密钥对，则验证通过。这样你就无需记忆和输入复杂的密码，只要保护好私钥这个文件不外泄即可。

> 密钥类型
>
> - RSA 密钥，在各种设备、SSH 客户端中有广泛悠久的支持历史，目前依然能够提供足够的安全性，它是大多数人的选择，但并非最优选择。
>
> - DSA 密钥，已经从数学层面被证明不安全，永远不要使用它。
>
> - ECDSA 密钥，密钥小且安全性高，但其算法被指留有 NSA 的后门，如果你的 VPS 上有值得 NSA 关注的东西就不要用它。
>
> - EdDSA 密钥，这是一个与 ECDSA 十分相似的算法，故具有相似的性能优势。同时其文档完全公开，所以普遍认为无后门。
>
> 如果你的设备软件都支持的话，建议优先使用 EdDSA 密钥，常见 EdDSA 密钥为 255 位的 Ed25519。

> 生成 SSH 密钥的方式
>
> 1. 通常 SSH 客户端软件都带有密钥生成器，可以可视化地生成密钥。例如 MobaXterm 中在 Tools 菜单下，打开 MobaKeyGen 即可生成密钥。
> 2. 通过命令行生成，Windows 10 1809 及以后的版本、Windows 11 都在系统中集成 OpenSSH 功能，默认开启 OpenSSH-Client 端，若未开启则可在 Windows 设置中开启，其他版本的系统可以通过安装 OpenSSH 获得密钥生成能力，如果你安装了 git，那么也会默认安装 OpenSSH。

> [ssh-keygen](https://www.ssh.com/academy/ssh/keygen#command-and-option-summary)
>
> ssh 密钥生成命令，安装好 OpenSSH 后就能执行 ssh-keygen 命令。
>
> ssh-keygen 命令常用参数
> -b “Bits”用于指定密钥位数，例如 RSA 密钥可指定位数，默认为 2048 bits，可通过该参数设置为 3072 bits 或 4096bits 等
> -t “Type”用于指定密钥算法类型，默认为 rsa，建议使用 ed25519
> -f “File”用于指定密钥文件目录与名称，默认生成在用户目录的.ssh 目录下，如`C:\Users\用户目录\.ssh\密钥名称`
> -c “Comment”用于指定密钥的注释，默认会使用`用户名@计算机名`作为注释

- 第一步【生成密钥】：

  可视化生成方式比较简单，且各软件可视化界面有不同，这里我们以命令行的方式举例说明，如何安装 OpenSSH 不作展开，假设已安装。

  Windows 系统上打开 cmd 或 powershell，执行：

  ```shell
  ssh-keygen -t ed25519
  ```

  > 注意
  >
  > 以默认 2048 位的 RSA 密钥为例，要想获得同 Ed25519 一样的安全性，需要使用 3072 位的 RSA 密钥，如：`ssh-keygen -t rsa -b 3072`

  按提示选择存储路径与文件名，再按提示输入密码短语。

  > 注意
  >
  > 如果不想每次登录都输入密码短语，可以直接回车，不设置密码短语，这样虽然方便，但是也不符合安全建议，如果不是一个人使用这台电脑，强烈建议设置私钥密码短语，防止私钥丢失造成的损失。

  命令执行完成后会在用户目录下生成`id_ed25519`与`id_ed25519.pub`两个文件（如果你使用默认路径与名称，未修改的话），后缀为 pub 的即为公钥，复制一份`id_ed25519.pub`并重命名为`authorized_keys`。

- 第二步，【上传密钥】

  使用 admin 账户，通过 MobaXterm 远程登录服务器，登录成功后，左侧文件夹栏就可以可视化地上传下载文件（相当于 WinSCP 功能）。

  登录后，默认就在 admin 用户文件下，新建`.ssh`文件夹，将刚刚复制的`authorized_keys`文件上传至`.ssh`文件夹下。

  修改`authorized_keys`文件权限为`600`（仅所有者可读可写）

  ```shell
  chmod 600 ~/.ssh/authorized_keys
  ```

- 第三步，【启用密钥验证登录】并【关闭密码验证登录】

  修改 SSH 配置，我们已经在前面做了很多次，但是现在我们已经从无所不能的 root 用户变成了普通的 admin 用户，普通用户是没有权限编辑 SSH 配置的，这时候就用到了 sudo 命令：

  ```shell
  sudo nano /etc/ssh/sshd_config
  ```

  找到（`Ctrl+w`）`PasswordAuthentication`改为`no`（如果你是第一次尝试，害怕出现问题把自己关在外面，可以先不禁用密码登录，等密钥登录成功后再禁用密码登录）。

  找到（`Ctrl+w`）`PubkeyAuthentication`改为`yes`，然后保存（`Ctrl+o`）退出（`Ctrl+x`）。

  重启 SSH 服务

  ```shell
  sudo systemctl restart ssh
  ```

- 第四步，【SSH 客户端配置使用私钥登录】

  前面我们已经做好了 VPS 端的公钥配置，只要在 MobaXTerm 客户端中配置好私钥，即可完成使用密钥登录了。

  右键已保存的 VPS 会话，点击 edit session，选择 SSH 大类下的 Advanced SSH settings，钩选 Use Private key，并选择和公钥配对的私钥，然后保存即可。

  保存成功即可双击会话一键登录了，当然如果你给私钥设置了密码短语，登录时还要输入密码短语才能使用私钥。

  现在你的 VPS 已经完成了【端口】、【用户名】、【密码】这三个要素的基本安全保障，虽然远称不上固若金汤，但一般的恶意脚本已经无法对你造成伤害了。

## 网站建设

现在我们已经有了一个安全的 VPS，那么 VPS 可以用来做什么呢？那么最简单最直观的例子无疑就是建立一个全世界都能访问到的网站。一个最简单的网站就是没有数据库与后端程序的前端静态站点，把前端静态站点的文件放在一个网页服务器程序上，就可以让别人访问了。

### 安装网页服务器程序

当下最知名的网页服务器程序要属[Nginx](https://nginx.org/)了，这里我们以 Nginx 网页服务器为例。你也可以选择现在风头正劲的[Caddy 2](https://caddyserver.com/)做为你的网页服务器。

- 登录 VPS 并安装运行 Nginx

  ```shell
  sudo apt update && sudo apt install nginx
  ```

  安装完成后，Nginx 已经自动运行。此时在浏览器中输入你的 VPS IP 地址`http://你的VPS IP地址:80`，就会看到 Welcome to nginx 的网页，这说明你的 Nginx 网页服务器已经正常运行了。

- 创建一个最简单的网页

  【在用户目录下创建一个网站专用的文件夹】并【新建网页文件 index.html】

  ```shell
  mkdir -p ~/www/webpage/ && nano ~/www/webpage/index.html
  ```

  > mkdir
  >
  > 创建文件夹命令，-p 参数表示递归创建，若不加-p 参数，则需要手动一层一层创建

  > 注意
  >
  > 务必理解命令中“~”的意义，它表示当前用户的用户目录，即：
  >
  > - 如果是【非 root 用户】，`~`表示`/home/用户名`
  > - 如果是【root 用户】，`~`表示`/root`

  复制下面的内容到打开的编辑器中，然后保存（`Ctrl+o`）并退出（`Ctrl+x`）。

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Enter a title, displayed at the top of the window.</title>
    </head>
    <body>
      <h1>Enter the main heading, usually the same as the title.</h1>
      <p>Be <b>bold</b> in stating your key points. Put them in a list:</p>
      <ul>
        <li>The first item in your list</li>
        <li>The second item; <i>italicize</i> key words</li>
      </ul>
      <p>Improve your image by including an image.</p>
      <p>
        <img src="https://i.imgur.com/SEBww.jpg" alt="A Great HTML Resource" />
      </p>
      <p>
        Add a link to your favorite
        <a href="https://www.dummies.com/">Web site</a>. Break up your page with
        a horizontal rule or two.
      </p>
      <hr />
      <p>
        Finally, link to <a href="page2.html">another page</a> in your own Web
        site.
      </p>
      <!-- And add a copyright notice.-->
      <p>&#169; Wiley Publishing, 2011</p>
    </body>
  </html>
  ```

- 修改 Nginx 配置文件并重启 Nginx 服务，将 80 端口的 http 访问定位到我们新建的 html 页面文件上

  > Nginx 配置文件
  >
  > nginx.conf 是 Nginx 服务器的配置文件，路径为`/etc/nginx/nginx.conf`

  打开 nginx.conf

  ```shell
  sudo nano /etc/nginx/nginx.conf
  ```

  在`http{}`块内，新增下面的`server{}`块，然后保存（`Ctrl+o`）退出（`Ctrl+x`）。

  ```shell
  # 服务器配置1
  server {
    # 监听80端口
    listen 80;
    # 域名，通常配置为两个，使两种访问方式都正确回落到你的首页上
    server_name 你的域名.com www.你的域名.com;

    location / {
      # 服务器根目录，注意，使用你真实网页文件所在的路径
      root /home/admin/www/webpage;
      # 默认索引文件名
      index index.html;
    }
  }

  # 服务器配置2
  server {
    # 监听80端口
    listen 80;
    # 域名，这里配置一个子域名，对子域名的访问都将回落到这个server的配置下
    server_name 子域名.你的域名.com;

    location / {
      # 服务器根目录，注意，这里为了简便使用了和主站同样的路径，通常子域名下使用的是和主站不同的文件，使用你真实网页文件所在的路径替换
      root /home/admin/www/webpage;
      # 默认索引文件名
      index index.html;
    }
  }
  ```

  重载 Nginx 配置使其生效

  ```shell
  sudo systemctl reload nginx
  ```

此时通过浏览器访问你的域名`http://你的域名.com`，看到你自己创建的页面，则说明成功。

到这里，你已经成功建立起了属于自己的网站，如果你想做得更好，可以找一些建立静态站点的工具，也可以学习一些前端知识，完全自定义你的网站。

## 证书管理

刚刚访问自己的网站时，相信你也收到了浏览器的提示，你与此网站之间建立的连接不安全。

因为 HTTP 协议是明文传输协议，无法加密传输数据或校验数据完整性，也无法进行身份验证。HTTP 协议的缺陷，是导致数据泄露、数据篡改、流量劫持、钓鱼攻击等安全问题的重要原因。通过 HTTP 协议传输的所有通信数据都在网络中明文“裸奔”，任何第三方都可以轻松地拦截、窃取、篡改数据，或伪造服务器欺骗用户。而 HTTPS 在 HTTP 的基础上加入了 SSL/TLS 协议，依靠 SSL/TLS 证书来验证服务器的身份，并为客户端和服务器端之间建立“SSL 加密通道”，确保用户数据在传输过程中处于加密状态，同时防止服务器被钓鱼网站假冒，确保客户端和服务器之间的信息交互始终安全。

那么接下来我们要做的，是为我们的域名申请一个真实的 TLS 证书，使网站具备标准 TLS 加密的能力及 HTTPS 访问的能力。

> 注意
>
> 请不要轻易使用自签证书。它并没有让操作简单太多，但增加了无谓的风险（如中间人攻击）。

这里我们使用[acme.sh](https://github.com/acmesh-official/acme.sh)作为证书管理工具，它简单、轻量、高效，并且可以完成证书自动更新。

你也可以使用最知名的开源 CA（Certificate Authority）服务商[Let's Encrypt](https://letsencrypt.org/)推荐的证书管理工具[Certbot](https://certbot.eff.org/)来管理你的证书，这个工具文档也很齐全，也是很好的选择。

> 知名 CA 服务商对比表
>
> | CA            | MaxLifetime | ECC | Domain Count | Wildcard | NotAfter |
> | ------------- | ----------- | --- | ------------ | -------- | -------- |
> | Let's Encrypt | 90          | Yes | 100          | Yes      | No       |
> | ZeroSSL       | 90          | Yes | 100          | Yes      | Yes      |
> | Google        | 90          | Yes | 100          | Yes      | Yes      |
> | Buypass       | 180         | Yes | 5            | Paid     | No       |
> | SSL.com       | 90          | Yes | 2            | Paid     | No       |

近来 ZeroSSL 势头很猛，提供 1 年期的证书还有可视化 UI 管理证书，并在 21 年 8 月成为 acme.sh 默认 CA 服务，可以查看[ZeroSSL vs Let's Encrypt](https://zerossl.com/letsencrypt-alternative/)。不过 ZeroSSL 需要先邮箱注册，这里我们还是选择 Let's Encrypt。

我们申请的免费 TLS 证书通常只有 90 天有效期，不过证书管理工具都会在到期前自动帮我们更新证书。

### 安装 acme.sh

> wget
>
> wget 是非交互式网络下载工具，与 curl 相似却有不同侧重。大部分 Linux 发行版都有预装。

使用 wget 下载 acme.sh 安装脚本并导入 shell 进行安装

```shell
wget -O - https://get.acme.sh | sh
```

> 说明
>
> wget 中 `-O` 参数表示将下载的文件写入参数指定的路径与文件，`-O`后的`-`稍有特殊，是`./-`的缩写，文件名用`-`表示下载的文件使用标准输出，并禁用链接转换。所以`-O -`参数意为以标准输出、禁用链接转换的方式下载到当前目录。
>
> 详细说明见[GNU_Wget_Download-Options](https://www.gnu.org/software/wget/manual/wget.html#Download-Options)

运行`.bashrc`让 `acme.sh` 命令生效。实际安装结束后，`acme.sh`已经是全局命令了，此时只要重启一次 shell，就可以全局访问`acme.sh`。这一步的操作是为了省去重启 shell 的麻烦，直接将变量注入当前环境。

```shell
. .bashrc
```

> 说明
>
> source 命令，即点命令，可以使用英文`source`也可以使用`.`，意为在当前环境执行脚本，没有新建子 shell，脚本中新建或改变的变量都会保存在当前 shell 中。而直接执行文件`.bashrc`或使用 sh 命令执行文件`sh .bashrc`是一样的，都新建了子 shell，子 shell 新建或改变的变量，不会带回父 shell。

开启 acme.sh 自动升级

```shell
acme.sh --upgrade --auto-upgrade
```

### 申请测试证书

在正式申请证书之前，我们先用测试命令（`--test`）来验证是否可以成功申请，这样可以避免配置有误时，反复申请证书失败，超过 Let's Encrypt 请求频率上限，导致后面的步骤无法进行。

> 说明
>
> ECC 证书的主要优势在于它的 key size 更小，意味着同等大小下安全性的提升和加密解密速度的加快。如 ECC-256Bits 的强度相当于 RSA-3072Bits，何乐而不为？如果网站确实需要兼容古老设备的，也还是按需选择 RSA 证书。

> 单个域名证书、通配符（Wildcard）证书、SAN 证书
>
> 对于 SSL 证书而言，通常是一个域名对应一个证书，例如`你的域名.com`和`www.你的域名.com`需要两个不同的证书。这对于有多个子域名的网站就很不方便，因此就有了通配符证书，即一个证书可以覆盖下一级的子域（`*.你的域名.com`都被一个证书覆盖）。而随着时代的发展，一个人可能不止一个域名，有多个不同的域名需要证书，这又催生出了 SAN（Subject Alternate Name）证书，一个证书对应多个一级域名或子域名，甚至是通配符的泛域名。
>
> 现在 SA 机构会对单个域名颁发单域名证书，对多域名和通配符的情况，直接颁发 SAN 证书。
>
> 对于单个证书和多个普通证书的申请，可以使用 webroot 模式进行，即只要有已经在运行的网站根目录的写权限，会自动写入文件用于证书颁发时的验证。而对于泛域名的证书申请，由于不确定根目录是什么，故需要通过 DNS 的方式验证，确保你完全拥有该域名。这需要你首先在 DNS 服务商获得 API Key，使`acme.sh`能够通过 API 调用的方式增加一条 TXT 解析，用于验证域名。而手动添加 TXT 解析的方式虽然也能通过验证，但无法自动更新证书，因为每次更新的 TXT 值不一样，故不推荐。

- 单个域名的测试证书申请命令：

  ```shell
  acme.sh --issue --server letsencrypt --test -d 你的域名.com -w /home/admin/www/webpage --keylength ec-256
  ```

  > 参数说明
  >
  > --issue 申请签发证书参数
  > --server CA 服务商，这里选择 Let's Encrypt
  > --test 测试模式
  > -d 即 --domain 你的域名
  > -w 即 --webroot 表示 webroot 模式签发证书，参数为网站的根目录
  > -k 即 --keylength 域名证书 key 长度，选择 ec-256

- 多个域名的测试证书申请（多个域名使用同一个根路径，仍可用 webroot 模式）

  ```shell
  acme.sh --issue --server letsencrypt --test -d 你的域名 -d www.你的域名.com -w /home/admin/www/webpage --keylength ec-256
  ```

- 多个域名含有泛域名的测试证书申请

  - 首先需要获得 DNS 服务商的 API Key，这里仍然以 NameSilo 为例

    - 登录后，点击 【头像】 - 【My Account】
    - 右侧点击 【API Manager】
    - 钩选 【IP Address Restrictions】 并填入你 VPS 的 IP 后保存（确保只有你的 VPS 能使用 API）
    - 最下方钩选 【Submitting this form constitutes your acceptance of our API terms of use】
    - 点击 【generate】 生成 API Key （务必记录下来，因为网站不会再展示，后续重新生成，之前的 API Key 就无法使用了）

  - 在 VPS 的 shell 中导入 API Key 作为环境变量

    ```shell
    export Namesilo_Key="ca311045a80f00c7dc74b4"
    ```

  - 测试证书申请

    ```shell
    acme.sh --issue --server letsencrypt --test -d 你的域名.com -d "*.你的域名.com" --dns dns_namesilo --dnssleep 900 -k ec-256
    ```

    > 参数说明
    >
    > --dns DNS 模式申请证书 后面跟随内嵌支持的服务商参数（可在[官网查询](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)是否支持），NameSilo 参数为`dns_namesilo`，会自动调用刚刚的 API Key 去生成 TXT 解析
    > --dnssleep 写入解析后的等待时间秒数，NameSilo 解析生效时间为 15 分钟，故需要等待 900 秒再去验证

如果成功就证明你的域名、配置全部正确。仔细观察，会发现颁发证书的域名是`https://acme-staging-v02.api.letsencrypt.org`，`staging`可以理解为【测试服】。

如果这一步出错，就在命令最后加上`--debug`参数，查看详细的申请过程和具体的错误。

这一步完成后，就可以申请正式的证书了。测试证书无需删除，它会被正式证书覆盖。

### 正式证书申请

申请正式证书命令与测试相似，只要删掉`--test`参数，再增加`--force`参数即可。由于证书申请后，还需要安装，且证书到期前自动更新后，安装并非自动执行的，因此我们需要在证书申请时使用`--renew-hook`参数，让证书成功更新后执行 shell，自动安装，并重启网站服务。

- 首先我们来编写安装证书与重启 Web 服务的脚本，【在用户目录下新建证书目录 cert】并【新建脚本文件 cert-renew.sh】再【赋予脚本文件执行权限】：

  ```shell
  cd ~ && mkdir cert && nano ~/cert/cert-renew.sh
  ```

  这里以通配域名为例，将下面的内容复制到文件中，按你的域名稍作修改，然后保存（`Ctrl+o`）退出（`Ctrl+x`）：

  ```shell
  #!/bin/bash

  ~/.acme.sh/acme.sh --install-cert -d 你的域名.com -d "*.你的域名.com" --ecc --fullchain-file ~/cert/nginx.crt --key-file ~/cert/nginx.key
  echo "Xray Certificates Renewed"

  chmod +r ~/cert/nginx.key
  echo "Read Permission Granted for Private Key"

  sudo systemctl restart nginx
  echo "Nginx Restarted"
  ```

  给脚本文件增加可执行权限

  ```shell
  chmod +x ~/cert/cert-renew.sh
  ```

  脚本文件主要做了三件事：

  1. 将你域名对应的证书安装到你指定的目录；
  2. 给所有用户增加证书私钥的读权限（防止 Nginx 默认使用 Nginx 用户，无法读取私钥文件）；
  3. 重启 Nginx 服务，使证书生效；

  > 参数说明
  >
  > -i 即 --install-cert 安装证书，实际上仅仅是将申请到的证书复制到指定目录，必需配合 -d 参数使用，指定域名
  > --ecc 表示使用 ECC 证书
  > --fullchain-file 表示全链证书的安装路径
  > --key-file 表示证书私钥的安装路径
  >
  > 证书已经申请到了，为什么还要有一步安装（复制）的过程？直接去 acme.sh 生成的路径取用不可以吗？acme.sh 官方文档有云，申请证书的路径与命名方式仅供内部使用，未来伴随版本变化可能会更改。当你开启 acme.sh 自动更新，恰好某次更新更改了证书的命名与路径，就会造成你的 Web 服务器加密出现问题。因此安装证书的命令保证了你 Web 服务器使用证书的一致性。

  > 证书说明
  >
  > | 文件名        | 内容                                           |
  > | ------------- | ---------------------------------------------- |
  > | 你的域名.cer  | 服务端证书                                     |
  > | 你的域名.key  | 证书的私钥                                     |
  > | ca.cer        | CA 服务商的中间证书与根证书                    |
  > | fullchain.cer | 包括了 服务端证书、中间证书、根证书 的全链证书 |
  >
  > HTTPS 的认证原理是链式认证，从服务端证书-->中间证书-->根证书，都通过了才证明不是被中间人篡改过的网页，通常用户的浏览器都会装有知名的根证书及中间证书，只要你的服务端证书是客户浏览器已安装的中间证书商签发的，那么你只需要在 Web 服务器上部署服务端证书，但是如果客户的浏览器未安装给你签发证书的中间证书，或给中间证书签发的根证书，都会造成浏览器提示你的网站不安全，因此大多数人都会选择全链证书，虽然证书大了不少，但是兼容性最好。

- 接下来，我们【将默认证书商改为 Let's Encrypt】：

  ```shell
  acme.sh --set-default-ca --server letsencrypt
  ```

- 再【申请正式证书】：

  ```shell
  acme.sh --issue -d 你的域名.com -d "*.你的域名.com" --dns dns_namesilo --dnssleep 900 -k ec-256 --force --renew-hook "~/cert/cert-renew.sh"
  ```

  > 参数说明
  >
  > -f 即 --force 强制执行。在证书未到期前（测试证书不能使用，但未过期），程序会限制不允许更新证书，加上这个参数，就可以强制更新。
  > --renew-hook 证书成功更新后的钩子，renew hook 仅在后续更新（`-r`）成功后被调用，首次申请（`--issue`）并不会调用。这里用来执行安装证书与重启 Web 服务器的 shell 脚本。

- 由于`--renew-hook`仅在更新成功后执行，因此【首次申请证书后需要手动执行下 shell】

  ```shell
  ~/cert/cert-renew.sh
  ```

  > 说明
  >
  > 这里你也可以使用`--post-hook`在首次申请或后续更新证书后执行 shell（无论申请或更新证书成功或失败都会触发）。
  >
  > 不要使用`--reloadcmd`，因为它本义是让我们重启服务器的，触发时机是申请、更新、安装证书后，`--reloadcmd`参数也会被申请、更新、安装证书这三个操作重写，我们的脚本中的安装证书操作没有该参数，那么脚本执行一次后就会将`--reloadcmd`重置，导致下次更新脚本未执行。而如果我们在脚本中也加上`--reloadcmd`操作，就会造成安装证书时又触发脚本，会造成无限循环。
  >
  > 造成这个问题的本质原因是 acme.sh 官方将安装证书的命令排除在 60 天自动更新执行的命令之外，只能手动执行。可以参考官方关于各钩子执行时机的说明[acme.sh-Using pre hook post hook renew hook reloadcmd](https://github.com/acmesh-official/acme.sh/wiki/Using-pre-hook-post-hook-renew-hook-reloadcmd)。
  >
  > 你也可以像原文中那样使用 Linux 中非常有用的定时任务 crontab 来实现
  >
  > - 创建定时任务
  >
  > ```shell
  > crontab -e
  > ```
  >
  > - 将下面内容加在文件最后，保存退出即可
  >
  > ```shell
  > # 1:00am, 1st day each month, run `cert-renew.sh`
  > 0 1 1 * *   bash ~/cert/cert-renew.sh
  > ```

### 修改 Nginx 配置

HTTP 访问默认使用 80 端口，而 HTTPS 访问默认使用 443 端口，我们需要【配置 Nginx 网页到 443 端口，并使 HTTP 自动跳转 HTTPS】。

打开 Nginx 配置文件：

```shell
sudo nano /etc/nginx/nginx.conf
```

修改配置如下，完成后保存并退出：

```shell
server {
  listen 80;
  # 删除原网站配置，改为跳转https配置
  return 301 https://$http_host$request_uri;
}

server {
  listen 443 ssl;
  server_name 你的域名.com www.你的域名.com;

  # ssl证书地址
  ssl_certificate      /home/admin/cert/nginx.crt;  # 全链证书的路径
  ssl_certificate_key  /home/admin/cert/nginx.key; # 私钥文件的路径

  location / {
    root /home/admin/www/webpage;
    index index.html;
  }
}

server {
  listen 443 ssl;
  server_name 子域名.你的域名.com;

  # ssl证书地址
  ssl_certificate      /home/admin/cert/nginx.crt;  # 全链证书的路径
  ssl_certificate_key  /home/admin/cert/nginx.key; # 私钥文件的路径

  location / {
    root /home/admin/www/webpage;
    index index.html;
  }
}
```

最后重启 Nginx 服务

```shell
sudo systemctl restart nginx
```

再次访问你的网站，你会发现，你的网站已经有了小锁标志，成功实现了 HTTPS 访问。

## 完结，撒花！

到这里，你已经完整完成了服务器安全防护与网站建设的所有教程。这毫无疑问是一个巨大的胜利！

最后再次声明，本文改编自[Project X - 使用指南 - 快速入门文档 - 小小白白话文](https://xtls.github.io/document/level-0)。非常感谢原文作者，创作出这样一篇非常棒的教程，鞠躬.jpg！

> 参考资料
>
> [关于 sudo 命令的一些配置和使用技巧](http://kuanghy.github.io/2019/11/17/linux-sudo)
>
> [How to use ssh-keygen to generate a new SSH key](https://www.ssh.com/academy/ssh/keygen#command-and-option-summary)
>
> [Configuring HTTPS servers](https://nginx.org/en/docs/http/configuring_https_servers.html)
