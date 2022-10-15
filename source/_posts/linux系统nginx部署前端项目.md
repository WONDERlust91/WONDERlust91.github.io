---
title: linux系统nginx部署前端项目
date: 2019-07-05 13:40:57
tags:
  - nginx
  - linux
categories: linux
---

系统环境 centos7，本文中默认使用 root 用户，故没有 sudo 命令，实践中根据用户不同酌情添加。

## 安装 nginx 服务器

### 1. 在 yum 中安装 nginx 源

安装前置环境

```shell
yum install yum-utils
```

新建文件/etc/yum.repo.d/nginx.repo，内容为

```conf
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
```

默认使用 nginx-stable 的源，若要使用 nginx-mainline 的源，将 enabled 更改为 1。或直接使用以下命令

```shell
yum-config-manager --enable nginx-mainline
```

<!-- more -->

### 2. 安装 nginx

```shell
yum install nginx
```

## 启动 nginx 服务

常规起动 nginx 服务器的方法是使用 nginx 命令，在 CentOS7 系统中，nginx 安装程序已经自动为我们注册了一个 nginx 服务，可以直接使用 systemctl 命令启动 nginx，nginx 默认的配置文件监听了 80 端口，直接启动 nginx 服务就可以访问到 nginx 的示例页面。

```shell
systemctl start nginx
```

### 1. 防火墙端口放行

我们这时候通过服务器的 ip 地址去访问服务器其实是访问不到的，因为服务器的防火墙没有放行我们要访问的端口。

```shell
# --permanent 使设置永久生效
# --zone=public 指定了配置规则生效的域为public，防火墙预设了多个域，一般默认为public域
# --add-port=80/tcp 放行了80端口，协议为tcp协议（http协议的底层就是tcp协议）
firewall-cmd --permanent --zone=public --add-port=80/tcp
# 修改完防火墙后，要重启防火墙服务，服务名为firewalld
systemctl restart firewalld
```

这时候我们访问服务器的 ip 地址，发现已经可以访问到 nginx 的示例页面了。

### 2. 403 Forbidden

当示例页面可以访问了以后，我们就可以把自己做的页面放在 nginx 服务器上了，使用 sftp 将文件夹传到服务器上。

```shell
# sftp 常用命令
# 改变远程工作目录
cd
# 改变本地工作目录
lcd
# 查看远程工作目录
pwd
# 查看本地工作目录
lpwd
# 列出远程目录
ls
# 列出本地目录
lls
# 上传文件，-r表示递归文件夹
put -r 本地目录
# 下载文件，-r表示递归文件夹
get -r 远程目录
```

```shell
# 将网站页面文件上传到/home/www文件夹
# 新建并切换远程工作目录
cd /home
mkdir www
cd www
# 切换本地工作目录到打包后的项目父级目录中（目录结构为./dist/demo）
lcd dist
# 上传
put -r demo
```

上传完成后，修改/etc/nginx/conf.d/default.conf 默认配置文件的路径

```conf
# 仅显示了要修改的配置，其他略去
server {
  location / {
    root /home/www/demo;
  }
}
```

修改配置后，重启 nginx 服务

```shell
systemctl restart nginx
```

这时我们再去访问服务器，发现页面并没有如愿展示，而是显示了 403 Forbidden 错误页面。

出问题时查看错误日志永远是定位问题最快捷的解决方法，查看/var/log/nginx/error.log 文件，我们发现报错信息为 403 Forbidden ( 13: permission denied )，这意味着我们没有权限，此时应该从下面四个方向排查问题。

#### (1) 检查 root 指向的目录下是否缺少 index.html 文件

当网站项目根目录下缺失了 index.html 时就会导致 403 错误，当然我们的 demo 文件夹下是有 index.html 的，排除了这一点。

#### (2) 是否关闭了 SELinux

SELinux 是安全增强式的 linux，开启了 SELinux 常常会导致没有权限访问的情况，由于其配置麻烦，通常都是直接关闭的。

```shell
# 查看SELinux状态
getenforce # 得到值为enforcing，说明SELinux为开启状态
# 临时修改SELinux状态为permissive，该状态下只记录不拦截，系统重启后恢复到enforcing状态
setenforce 0
getenforce # 得到值为permissive
```

若要永久修改 SELinux 状态，则修改/etc/selinux/config，SELINUX=enforcing 改为 permissive 或 disable。permissive 记录了违反了安全规则的行为，但并不会拦截，而 disable 则是彻底关闭。

#### (3) web 网站页面所在的文件夹是否拥有读权限与执行权限

刚刚我们只是上传了 demo 文件夹，并没有更改权限，这也是导致 403 错误的一个原因。

```shell
# 修改demo文件夹权限
chmod 755 -R /home/www/demo
```

#### (4) nginx.conf 中用户与启动用户不一致

查看 nginx 的启动用户

```shell
ps aux | grep "nginx: worker process" | awk '{print $1}'
# nginx
# root
```

我们发现是 nginx 用户启动的，而我们现在使用的是 root 用户。这时就需要我们在/etc/nginx/nginx.conf 文件中将 user 由 nginx 改为 root。

注意，这里只是针对 403 错误的举例，通常生产环境使用时，不应该用 root 用户启动，会造成安全隐患，应该切换到 nginx 用户启动 nginx 服务。

## 单页面应用的配置

做到上面一步，我们已经能通过访问服务器，浏览原生网站了。然而事情并不是这么简单，现代前端开发，大量使用了单页面应用的框架，如 react、vue、angular 等，页面路由不再由后端控制，而是由前端来控制，那么服务器端就需要额外的配置来支持单页面应用的部署。

针对单页面应用配置的核心思想就是将所有对文件的查找都重定向到 index.html 由 index.html 中引用的 js 来处理所有路由匹配任务。

这里以 vue 举例，通常情况下，vue.config.js 中的 publicPath 配置为相对路径"./"适应性最强，但是一旦使用了 history 模式就不能使用相对路径了。所以我们只讨论绝对路径的两种情况，一种是在根路径"/"，一种是在子路径，如"/demo"。

### 在根路径"/"下

```conf
location / {
  root  /home/www/demo;
  index index.html index.htm;
  try_files $uri $uri/ /index.html;
}
```

### 在子路径，如"/demo"下

```conf
# 使用root指向网站目录
location /demo {
  root  /home/www;
  index index.html index.htm;
  try_files $uri $uri/ /demo/index.html;
}
# 使用alias指向网站目录
location /demo {
  alias  /home/www/demo;
  index index.html index.htm;
  try_files $uri $uri/ /demo/index.html;
}
```

### 两个服务器，反向代理的方式

```conf
# 服务器A
server {
  # 监听80端口
  listen 80;
  # nginx示例页面
  location / {
    root /usr/share/nginx/html;
    index index.html index.htm;
  }
  # 反向代理
  location /demo {
    proxy_pass http://localhost:8080/demo;
  }
}
```

```conf
# 服务器B
server {
  # 监听8080端口
  listen 8080;
  # nginx示例页面
  location / {
    root /usr/share/nginx/html;
    index index.html index.htm;
  }
  # 子目录
  location /demo {
    root /home/www;
    index index.html index.htm;
    try_files $uri $uri/ /demo/index.html;
  }
}
```

注意：

1. try_files 会匹配所有书写的路径，若全部匹配失败，则会跳转到最后一个目标路径，所以最后一个路径一定要是能访问到的；

2. 为了防止循环匹配，要在 vue 的路由中处理不存在的地址，因为 try_files 会使服务器处理不存在地址失效；

3. 使用 root 匹配是将 url 中的 host 部分替换为 root 中设置的路径，例如访问http://localhost/demo/index.html，匹配到了/demo，将demo前的http://localhost替换为root中设置的/home/www，就得到了要访问的硬盘真实地址/home/www/demo/index.html；

4. 使用 alias 匹配是将匹配到的部分整个替换，例如访问http://localhost/demo/index.html，匹配到demo，将包括demo在内的前面的所有地址http://localhost/demo都替换为alias设置的/home/www/demo，即得到了硬盘真实地址/home/www/demo/index.html；

5. nginx 配置文件在/etc/nginx/nginx.conf，nginx.conf 默认导入了/etc/nginx/conf.d 下的所有 conf 文件，因此只要在 conf.d 下新增后缀为 conf 的文件即可。每个文件都可以新增一个 server 块，监听不同的端口，不过要记得打开端口的防火墙。每次修改配置文件后都要重启 nginx 服务方可生效；

6. 单页面应用应避免部署在根目录下再进行反向代理，因为通过当前服务器 A 请求被代理的 B 服务器上的 index.html 时能够正常访问到代理服务器 B，但是 B 中的 index.html 请求资源时又没有从代理服务器 B 请求，而是请求到了当前服务器 A，当前服务器 A 下是没有资源文件的，就会请求失败。而如果在 B 服务器上将网站部署在子目录下，如/demo，通过当前服务器 A 请求被代理的 B 服务器上的 index.html，则能正确访问资源。

> 参考链接  
> [解决 Nginx 出现 403 forbidden (13:Permission denied)报错的四种方法](https://www.centos.bz/2018/12/解决nginx出现403-forbidden-13-permission-denied报错的四种方法)  
> [Vue 爬坑之路三：Nginx 部署](https://www.jianshu.com/p/05f889faa74b)  
> [Nginx 静态服务配置——详解 root 和 alias 指令](https://www.jianshu.com/p/4be0d5882ec5)  
> [centos7 防火墙配置整理](https://www.jianshu.com/p/5825b18c80a1)  
> [Nginx 的 try_files 参数保证能懂的讲解](https://blog.51cto.com/10546390/1754757)
