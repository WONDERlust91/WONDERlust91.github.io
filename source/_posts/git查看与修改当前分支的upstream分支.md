---
title: git查看与修改当前分支的upstream分支
date: 2021-10-11 10:48:29
tags:
  - upstream branch
  - 上游分支
categories: git
---

## 应用场景

公司的前端框架与项目本身存储于两个不同的远程仓库下，具体项目在创建时，基于前端框架的某个版本。开发一段时间后，若框架有比较重要的更新需要添加到项目中时，基于更新后的框架再将项目中的代码移植过去，通常比较麻烦。

实践下来，现在比较好的方案是给项目两个分支与两个远程仓库，其中一个分支与远程仓库用于项目的开发，另一个分支和远程仓库用于追踪框架的更新，当拉取框架更新后，将追踪框架更新的分支合并到项目分支中即可。这样只需要处理少量冲突，甚至不用处理冲突，就可以完成更新。

这个方案虽比较好用，但是有个风险就是如果忘记更改 dev 分支的上游分支，可能会把项目代码，错误地推送至框架代码库。那么如何方便地查看当前分支的远程上游分支呢？

## 查看当前分支的上游（upstream）分支

在 git branch 的帮助文档中-v 参数下有一行说明，如果给两个 v 字母，将会相比给一个 v，额外打印出上游分支的名称与链接的工作目录（如果链接的工作目录存在）。

即使用 `git branch -vv` 就可以查看所有分支的上游分支名称。

<!-- more -->

## 具体操作流程

1. clone 框架，`git clone git@gitlab.xxx.com:xxx/framework.git`
2. 拉取 dev 分支的最新更新，（因为公司的习惯，框架的最新更新通常都在 dev 分支上）`git checkout dev`
3. 重命名 dev 分支为 framework-dev，与项目的 dev 分支区分开，`git branch -m dev framework-dev`
4. 重命名远程仓库 origin 为 framework-origin，与项目的远程仓库 origin 区分开，`git remote rename origin framework-origin`
5. 基于 framework-dev，创建新的分支 dev，在 dev 上进行项目开发，`git checkout -b dev`
6. 增加项目的远程仓库 origin，`git remote add origin git@gitlab.xxx.com:xxx/project.git`
7. 查看所有分支的上游分支情况，`git branch -vv`，这时会发现 framework-dev 分支的上游正确指向了 framework-origin/dev，master 的上游也默认指向了 framework-origin/master，而新建的用于项目的 dev 分支没有默认上游分支。那么为了防止 master 分支错误推送到框架的 master 分支，则应删除 master 分支的上游分支；同时也要将 dev 的默认上游分支设置为 origin/dev
8. 删除 master 的上游分支，`git branch --unset-upstream master`
9. 添加 dev 的上游分支，`git branch -u origin/dev`；如果还未在远程仓库提交过，可能这一步操作会失败，因为远程与本地不一致，此时可以先向远程仓库提交一次，并在提交过程中设置远程为上游分支`git push -u origin dev`

执行完上面步骤，就可以在 dev 上执行 git push 而不用担心错误提交到框架的仓库中了。日后的使用过程中，也只需要在 framework-dev 分支中拉取框架的更新，再将 framework-dev 合并到 dev 分支即可完成更新。

> 参考资料
>
> [Git 远程 03：分支的 upstream](https://higoge.github.io/2015/07/06/git-remote03/)
>
> [git 如何查看当前分支的 upstream?](https://www.zhihu.com/question/27324031)
>
> [Renaming a remote repository](https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories#renaming-a-remote-repository)
