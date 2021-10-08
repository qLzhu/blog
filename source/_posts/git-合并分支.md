---
title: Git Rebase Branch
abbrlink: 31307
date: 2021-10-06 20:04:55
categories: git
tags: [git, git merge, git rebase]
---

在测试 CI 自动化部署时，我们提交了很多测试附注，为了保持线上 LOG 的清洁，我们需要把这些信息合并成一个 COMMIT 进行提交，首先通过 LOG 命令查看我们都需要合并哪些 COMMIT 附注。

```bash
git log --oneline
```

通过查看 LOG 得知我需要合并 23 条 COMMIT

```bash
git rebase -i HEAD~23
```
<!-- more -->

执行后，GIT 会自动使用默认的编辑器帮我们打开此附注，让我们重新编辑，把需要合并的 COMMIT 信息前的 pick 改成 s，:wq! 保存退出即可

附注”
如果在终端模式下使用 :wq! 无法退出编辑模式的话
那是你没有设置 GIT 默认编辑器的原因
请查阅我之前写的 {% post_link git-设置默认编辑器 《设置 GIT 默认编辑器》%} 一文

```bash
pick 2231360 some old commit
s ee2adc2 Adds new feature


# Rebase 2cf755d..ee2adc2 onto 2cf755d (9 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

然后执行 `git push` 命令提交到远程仓库。可是，到这里我出错了，终端给出的提示是

```bash
 ! [rejected]        HEAD -> master (non-fast-forward)
error: 推送一些引用到 'git@github.com:qLzhu/blog.git' 失败
提示：更新被拒绝，因为推送的一个分支的最新提交落后于其对应的远程分支。
提示：检出该分支并整合远程变更（如 'git pull ...'），然后再推送。详见
提示：'git push --help' 中的 'Note about fast-forwards' 小节。
```

原因是：

我们合并多个附注后，GIT 为我们自动创建了一个 418e85d 分支，此分支跟远程仓库并没有保持同步，所以报错

```bash
# 当前处于叉劈的 418e85d 分支上
$blog [418e85d] git push origin HEAD:master --force
```

这时我们就需要使用 Merge 命令，把劈叉的 418e85d 分支合并到 master 上了

```bash
git checkout master
git merge 418e85d
git push origin HEAD:master --force
```

https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase