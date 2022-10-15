---
title: git命令之rebase
date: 2021-07-07 11:33:40
tags:
  - git
  - rebase
categories: git
---

## 使用场景一：合并提交记录

### 一个功能有很多次 commit，会存在的问题

1. 不利于代码 review，一个较小的功能却分为多次提交，review 时需要切换提交且不方便代码对比;

2. 一旦线上环境出现问题，需要回滚代码时，大量的 commit 记录，也不方便查找。

另外如果是在 github 上维护一个开源库，很多杂乱的提交记录也并不友好。使用 rebase 命令可以合并多次提交记录，使提交记录变得清晰整洁。

<!-- more -->

### 合并提交记录的操作

- 合并提交记录时，通常会使用 rebase 的交互模式，即`-i`参数（全称是`--interactive`参数), 表示提交记录的参数则有两种形式，一种是`HEAD~n`，表示操作倒数第 n 次提交**及**其后的所有提交（`HEAD~n`实际上表示的是第 n 代祖先提交，这里不考虑有分叉的情况，假设提交是一条线，如`HEAD~2`是第 2 代祖先提交，即倒数第三个提交，当前提交为`HEAD~0`，而 rebase 操作指的是`HEAD~2`后的提交，不包含`HEAD~2`，故为`HEAD~1`和`HEAD~0`，即倒数两次的提交）；还有一种是形如`0a93cf`的提交 hash 值，表示要操作的是`0a93cf`这个提交后（时间上）的所有提交，不包含`0a93cf`本身。

```bash
# rebase操作0a93cf后的所有提交
git rebase -i 0a93cf
# rebase操作最近2次提交
git rebase -i HEAD~2
```

- 执行完上面的命令后，会自动打开编辑器，内容如下：

```bash
# 每个提交默认操作都是pick，可以使用下面注释中提示的缩写修改每个提交的操作
pick 15447ca add: test2
s 1e12203 add: test4

# Rebase 0a93cfd..1e12203 onto 0a93cfd (2 commands)
#
# Commands:
# p, pick <commit> = use commit 使用该条提交
# r, reword <commit> = use commit, but edit the commit message 使用该条提交，但需要编辑提交信息
# e, edit <commit> = use commit, but stop for amending  使用该条提交，但需要暂停进程并修改代码
# s, squash <commit> = use commit, but meld into previous commit 使用该条提交，但合并入上一条提交；注意由于第一条没有上一条，故不能在第一条上使用s命令
# f, fixup <commit> = like "squash", but discard this commit's log message 与squash相似，但不保留提交信息
# x, exec <command> = run command (the rest of the line) using shell 需要执行shell命令
# b, break = stop here (continue rebase later with 'git rebase --continue') 停止到这个提交，后续通过`git rebase --continue`命令继续
# d, drop <commit> = remove commit 丢弃该提交
# l, label <label> = label current HEAD with a name 给当前HEAD一个label名称
# t, reset <label> = reset HEAD to a label 将HEAD重置为某个label标签
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>] 创建一个合并提交，使用原始合并提交信息
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

- 如果保存出现了错误或异常退出了编辑器，此时可以使用`git rebase --edit-todo`命令，重新打开编辑器。

- 如果有暂停 break 或修改代码 edit 等操作时，rebase 流程会暂停，需要使用`git rebase --continue`继续。

- 最后可以通过`git log`命令查看结果，发现提交记录已合并。

- 若想撤回 rebase 怎么办？log 中已经没有合并前的 hash 了。此时可以使用`git reflog`命令，查看所有操作记录找回 rebase 前的 hash，并通过`git reset`恢复到 rebase 前。

## 使用场景二：分支合并

### merge 合并与 rebase 合并的区别

通常我们使用`merge`命令进行分支合并，`merge`命令的一个显著特点是将两个分支的最新快照与他们最近的共同祖先进行三方合并，并将合并的结果生成一个新的快照，并以制式信息自动提交。

而`rebase`命令是将提交到某一分支上的所有修改都移至另一分支上，好像“重新播放”一样。

例如：

```bash
# 检出至dev分支
git checkout dev
# dev变基为master
git rebase master
```

原理为先找到当前分支 dev 与目标基底分支 master 的最近共同祖先，然后对比当前分支 dev 相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目标基底分支 master 的最新提交，最后基于 master 的最新提交将之前保存的临时文件依序应用提交。

使用 rebase 与 merge 在最终结果上没有本质的区别，只是 rebase 变基使得提交历史更加整洁。尽管实际的开发工作是并行的，但在 rebase 后的提交历史中，它们看起来像是串行的一样，提交历史是一条直线没有分叉。

### 为什么使用 rebase 合并

一般情况下，我们这样做的目的是为了确保在向远程分支推送功能时保持提交历史的整洁，尤其是向他人维护的项目贡献代码时。在这种情况下，你首先在自己的分支里进行开发，开发完成后你需要先将你的代码变基到`origin/master`上，然后再向主项目提交修改。这样，项目维护者就不再需要进行整合工作，只需要快进合并即可。

### 更复杂的变基例子

#### onto 参数

假设现在有三个分支：master、next、topic，三个分支关系如下：

```txt
    o---o---o---o---o  master
         \
          o---o---o---o---o  next
                           \
                            o---o---o  topic
```

topic 分支基于 next 分支，但此时想要将 topic 分支的修改合并入 master 分支，但还不想合并 next 分支，那么可以执行命令`git rebase --onto master next topic`。

这个命令的意思是：取出 topic 分支，找出它从 next 分支分叉后的修改，然后把这些修改在 master 分支上重放一遍，让 topic 分支像直接基于 master 修改一样。

命令执行后的结果如下：

```txt
    o---o---o---o---o  master
        |            \
        |             o'--o'--o'  topic
         \
          o---o---o---o---o  next
```

#### 直接变基

接下来，假设你决定将 next 分支也合并进 master 中，但你当前分支并不是 next 分支，那么使用`git rebase <baseBranch> <topicBranch>`命令即可，例如：`git rebase master next`，这个命令可以直接将主题分支（next）变基到目标分支（master）上。这样能省去切换到 next 分支再变基的步骤。

```bash
# 当前分支为topic，要合并next分支到master上
# 方式1
git rebase master next
# 方式2
git checkout next
git rebase master
```

#### 依靠 rebase 删除提交

例如有如下提交记录：

```txt
    E---F---G---H---I---J  topicA
```

执行命令`git rebase --onto topicA~5 topicA~3 topicA`，那么结果为：

```txt
    E---H'---I'---J'  topicA
```

F 提交与 G 提交被删除了。这条命令的意思是：将 topicA 的所有提交基于 topicA 第三个祖先提交（G）的变化（H-I-J）提取出来，再以 topicA 第 5 个祖先提交（E）为基底变基，故 F、G 两个提交就通过变基的操作删除掉了。

## 变基的风险

使用变基得遵守一条准则：**如果提交已经存在于你的本地仓库之外（远程仓库），而别人可能已经基于这些提交进行开发，那么不要执行变基。如果一定要执行变基，那么请确保通知每一个人执行`git pull --rebase`命令。**

- 例如你基于远程版本库 C1 进行开发，你在本地提交了 C2、C3。

```txt
    C1 origin/master 远程库
     \
      C2---C3 master 本地库
```

- 远程库也有新的修改和合并提交。

```txt
      ---C5---
     /        \
    C1---C4---C6 origin/master 远程库
```

- 你拉取了这些提交，并执行了合并提交。

```txt
      ---C5---
     /        \
    C1---C4---C6 origin/master 远程库
     \         \
      C2---C3---C7 master 本地库
```

- 远程库的提交人将合并操作回滚，改用变基；又用`git push --force`强行覆盖了服务器上的提交历史。

```txt
    C1---C5---C4' origin/master
```

- 此时你再拉取服务器上的提交，你会多出来新的提交。而并不会删除服务器上已经删除的提交。

```txt
      ---C5---C4' origin/master 远程库
     /       \    \
    C1---C4---C6   \
     \         \    \
      C2---C3---C7---C8 master 本地库
```

- 可以发现此时，你有两个一样的 C4 提交，如果你再将这样的版本库推送到服务器上，实际上是将那些已经被变基抛弃的提交（C4、C6）又找了回来。

那么真的遇到这种情况怎么解决呢？我们已知远程分支被变基的情况下，可以手动拉取变更`git fetch`再执行变基`git rebase origin/master`，或者使用`git pull --rebase`而不是直接使用`git pull`。

如果习惯使用`git pull`，同时又希望默认使用`--rebase`选项，可以执行这条语句`git config --global pull.rebase true`来更改`pull.rebase`的默认配置。

### 可参考的实践

基于变基的风险，也有人总结出了一套实践原则，可以供大家参考：

1. 下游分支更新上游分支内容时，使用 rebase 合并;
2. 上游分支更新下游分支内容时，使用 merge 合并;
3. 当前分支拉取更新时，使用`--rebase`选项;

> 参考资料
>
> [Git 分支-变基](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)
>
> Git 帮助文档`git rebase --help`
>
> [(Git)合并多个 commit](https://segmentfault.com/a/1190000007748862)
>
> [GIT 使用 rebase 和 merge 的正确姿势](https://zhuanlan.zhihu.com/p/34197548)
>
> [学会 git-rebase 看这一篇就可以了](https://segmentfault.com/a/1190000030688343)
>
> [彻底搞懂 Git-Rebase](http://jartto.wang/2018/12/11/git-rebase/)
