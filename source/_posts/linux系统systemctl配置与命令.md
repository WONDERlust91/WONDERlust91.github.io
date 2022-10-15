---
title: linux系统systemctl配置与命令
date: 2018-04-08 17:56:09
tags:
- linux
- systemctl
categories: linux
---
# systemctl配置文件

CentOS 7的服务systemctl脚本存放在/usr/lib/systemd/目录下，/usr/lib/systemd/system目录下是不需要用户登陆，开机就能运行的服务脚本，/usr/lib/systemd/user目录下是用户登陆后才能运行的服务脚本。

服务脚本由以下三部分组成

## [Unit] 部份

unit区块通常是配置文件的第一个区块，用来定义Unit的元数据，以及配置与其他Unit的关系。它的主要字段如下。

| 设置参数 | 参数意义说明 |
| - | - |
| Description | 简易说明，用于使用时的输出说明和systemctl status的输出说明。 |
| Documentation | 文档地址，提供管理员进行进一步的文件查询的功能。 |
| After | 此unit是在哪个daemon启动之后才启动，仅说明服务启动的顺序，并没有强制要求里头的服务一定要启动后此 unit 才能启动。 |
| Before | 与After的意义相反，是在什么服务启动前启动这个服务。仅规范服务启动顺序，非强制要求。 |
| Requires | 定义与此unit相依赖的其他服务，如果在此项设置的依赖服务没有启动，那么此unit就不启动。 |
| BindsTo | 与Requires类似，定义与此unit相绑定的其他服务，若此项设置的绑定服务退出，则该unit也退出。 |
| Wants | 与该unit配合的其他服务，若其他服务未运行，该服务仍能启动。 |
| Conflicts | 与该unit冲突的服务，即冲突服务若已启动，该unit则不能启动。该unit已启动，则冲突服务就不能启动。 |
| Condition... | 当前 Unit 运行必须满足的条件，否则不会运行。 |
| Assert... | 当前 Unit 运行必须满足的条件，否则会报启动失败。 |
<!-- more -->
## [Service] 部份

service区块用来定义Service的配置，只有Service类型的Unit才有这个区块。它的主要字段如下。

| 设置参数 | 参数意义说明 |
| - | - |
| Type | 定义这个进程启动的方式，会影响ExecStart。有以下几种类型。simple：默认值，执行ExecStart的指令串，启动主进程，启动后常驻于内存中。forking：以fork方式从父进程创建子进程，创建后父进程会立即退出。传统的unit服务大多属于这种类型，例如httpd服务，当httpd的程序因为运行过久即将终结，则systemd会再重新生出另一个子程序继续运行后，再将父程序删除。oneshot：一次性进程，Systemd会等当前服务退出，再继续往下执行，不过这个程序在工作完毕后就结束了，不会常驻在内存中。dbus：与simple类似，当前服务通过D-Bus启动，即这个服务必须要在取得一个D-Bus的名称后，才能运行。因此设置这个项目时，通常也要设置BusName= 。idle：与simple类似，若其他服务执行完毕，当前服务才会运行。即这类的服务通常是开机到最后才执行的服务！ notify：当前服务启动完毕，会通知Systemd，再继续往下执行。 |
| ExecStart | 启动当前进程的指令或脚本程序。你也可以使用ExecStartPre（之前）以及ExecStartPost（之后）两个设置项目来在实际启动服务前（后），进行额外的指令行为。 但要特别注意的是，指令串仅接受“指令 参数 参数...”的格式，不能接受<, >, >>, &#124;, &等特殊字符，很多的bash语法也不支持，故使用这些特殊的字符时，应直接写入到指令脚本中。 |
| ExecStop | 与systemctl stop的执行有关，关闭此服务时所执行的指令。 |
| ExecReload | 与systemctl reload有关的指令行为，重启当前服务时执行的命令。 |
| Restart | 定义何种情况Systemd会自动重启当前服务，可能的值包括always（总是重启）、on-success、on-failure、on-abnormal、on-abort、on-watchdog。当设置 Restart=always时，则此daemon服务终止后，会再次的启动此服务。举例来说，如果你在tty2使用文字界面登陆，操作完毕后登出，这个时候tty2就已经结束服务了。 但是你会看到屏幕又立刻产生一个新的tty2的登陆画面等待你的登陆。那就是Restart的功能。除非使用systemctl强制将此服务关闭，否则这个服务会源源不绝的一直重复启动。 |
| RestartSec | 自动重启当前服务间隔的秒数。默认是100ms（毫秒）。 |
| TimeoutSec | 若这个服务在启动或者是关闭时，因为某些缘故导致无法顺利“正常启动或正常结束”的情况下，则我们要等多久才进入“强制结束”的状态。 |
| Environment | 指定环境变量。 |
| PrivateTmp | 布尔值，是否使用私有临时文件夹，建议true，防止临时文件相互读写，提升安全性。 |
| RemainAfterExit | 当设置RemainAfterExit=true时，则当这个daemon所属的所有程序都终止之后，此服务会再尝试启动。这对于Type=oneshot的服务很有帮助。 |
| KillMode | 可以是process，control-group，none的其中一种，如果是process，则daemon终止时，只会终止主要的程序（ExecStart 接的后面那串指令），如果是control-group， 则由此daemon所产生的其他control-group的程序，也都会被关闭。如果是none的话，则没有程序会被关闭。|

# [Install] 部份

通常是配置文件的最后一个区块，用来定义如何启动，以及是否开机启动。它的主要字段如下。

| 设置参数 | 参数意义说明 |
| - | - |
| WantedBy | 它的值是一个或多个Target，当前 Unit 激活时（enable）符号链接会放入/etc/systemd/system目录下面以Target名 + .wants后缀构成的子目录中。通常，大多的服务性质的unit都是附挂在multi-user.target下面。 |
| RequiredBy | 它的值是一个或多个Target，当前Unit激活时，符号链接会放入/etc/systemd/system目录下面以Target名 + .required后缀构成的子目录中。 |
| Also | 该unit被激活为enable时，Also后的unit也被激活为enable。 |
| Alias | 创建一个unit的链接别名。systemctl enable相关的服务时，则此服务会进行链接文件的创建。以multi-user.target 为例，当设置default.target为别名时，/usr/lib/systemd/system/multi-user.target会创建名为/etc/systemd/system/default.target的别名链接。 |

# systemctl 基本命令
```bash
# 列出正在运行的 Unit
$ systemctl list-units

# 列出所有Unit，包括没有找到配置文件的或者启动失败的
$ systemctl list-units --all

# 列出所有没有运行的 Unit
$ systemctl list-units --all --state=inactive

# 列出所有加载失败的 Unit
$ systemctl list-units --failed

# 列出所有正在运行的、类型为 service 的 Unit
$ systemctl list-units --type=service

# 显示系统状态
$ systemctl status

# 显示单个 Unit 的状态
$ sysystemctl status bluetooth.service

# 显示远程主机的某个 Unit 的状态
$ systemctl -H root@rhel7.example.com status httpd.service

# 显示某个 Unit 是否正在运行
$ systemctl is-active application.service

# 显示某个 Unit 是否处于启动失败状态
$ systemctl is-failed application.service

# 显示某个 Unit 服务是否建立了启动链接
$ systemctl is-enabled application.service

# 立即启动一个服务
$ sudo systemctl start apache.service

# 立即停止一个服务
$ sudo systemctl stop apache.service

# 重启一个服务
$ sudo systemctl restart apache.service

# 杀死一个服务的所有子进程
$ sudo systemctl kill apache.service

# 重新加载一个服务的配置文件
$ sudo systemctl reload apache.service

# 重载所有修改过的配置文件
$ sudo systemctl daemon-reload

# 显示某个 Unit 的所有底层参数
$ systemctl show httpd.service

# 显示某个 Unit 的指定属性的值
$ systemctl show -p CPUShares httpd.service

# 设置某个 Unit 的指定属性
$ sudo systemctl set-property httpd.service CPUShares=500

# 列出一个 Unit 的所有依赖
$ systemctl list-dependencies nginx.service

# 上面命令的输出结果之中，有些依赖是 Target 类型（详见下文），默认不会展开显示。如果要展开 Target，就需要使用--all参数。
$ systemctl list-dependencies --all nginx.service
```
本文摘自
>http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html

>https://www.jianshu.com/p/0e8410049ee8