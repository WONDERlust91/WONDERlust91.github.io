---
title: 如何用git配合github或VPS做代码仓库
date: 2018-02-21 13:09:59
tags: 远程仓库
categories: git
---
虽然git做为版本控制工具，最大的特点是分布式多人协作，但是对个人开发者来说git除了用来版本控制，还可以当作中心版本库，用来在各个设备间同步代码，省去了用U盘和云盘管理代码的麻烦。
<!-- more -->
## 在VPS上建立代码仓库
1. 在VPS上建立新文件夹，并初始化为裸版本库。
```bash
git init --bare
```

    注：VPS上的版本库只用来做为同步的中心版本库， 并不用于开发，故建立为裸版本库。

2. 在本地PC上的开发版本库目录中，添加远程版本库。
```bash
git remote add remoteNickname ssh://username@ipAddress(domainName):port/fileAddress
```
3. 将本地PC上的开发版本库的分支推送至远程中心版本库
```bash
git push origin master #将master分支推送至origin
```

例:  
在远程VPS上
```bash
mkdir /root/foo #在VPS上建立foo文件夹
cd /root/foo #以foo为当前工作目录
git init --bare #在foo中建立裸版本库
```
在本地PC上
```bash
cd c:/foo #假设在开发版本库在本地c盘的foo中，以foo为前工作目录

git remote add origin ssh://root@192.169.1.1:28888/root/foo
#远程目录别名一般为origin，用root账户登录ssh，192.169.1.1为你VPS的IP地址，28888为你的ssh端口，/root/foo则是第1步中裸版本库的文件地址

git push origin master #将master分支推送至origin
```
## 在github上建立代码仓库
1. 在github网站上建立新的仓库，获取仓库的github链接，可以是https链接也可以是ssh的git链接，两种链接处理方式不同。
2. 在本地添加远程版本库。  
如使用https协议，则会提示输入github账户密码；  
如使用git协议（ssh），则应在本地PC的bash中运行ssh-keygen -t rsa后，将用户目录下的隐藏文件夹.ssh中的id_rsa.pub的文件内容复制到github设置中的ssh key管理页面。
```bash
#https协议
git remote add remoteNickname https://github/yourUsername/yourRepository.git

#git（ssh）协议
ssh-keygen -t rsa #生成ssh公钥
#将ssh公钥在github设置好后
git remote add remoteNickname git@github/yourUsername/yourRepository.git
```
3. 将本地版本库的一个分支推送至github。
```bash
git push remoteNickname master
```
注意：向远程版本库推送时如果报错，可以尝试先拉取远程版本库的更新再进行推送。
```bash
git pull remoteNickname master
```
如果在创建github仓库时勾选了自动创建md文档，则第一次pull会失败，因为本地仓库与github仓库不是同一个base，在拉取时应使用--allow-unrelated-histories参数
```bash
git pull remoteNickname master --allow-unrelated-histories
```