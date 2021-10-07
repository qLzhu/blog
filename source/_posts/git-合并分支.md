---
title: GIT 合并分支并提交
abbrlink: 31307
date: 2021-10-06 20:04:55
categories: git
tags: [git, git merge]
---

在 `git rebase -i HEAD~23` 合并多个 commit 后，使用 `git push origin HEAD:master` 强制提交时报

```bash
 ! [rejected]        HEAD -> master (non-fast-forward)
error: 推送一些引用到 'git@github.com:qLzhu/blog.git' 失败
提示：更新被拒绝，因为推送的一个分支的最新提交落后于其对应的远程分支。
提示：检出该分支并整合远程变更（如 'git pull ...'），然后再推送。详见
提示：'git push --help' 中的 'Note about fast-forwards' 小节。
```

原因是我们合并多个附注后，GIT 为我们创建了一个分支，此分支跟远程仓库并没有保持同步，所以报错

```bash
# 当前处于叉劈的 418e85d 分支上
$blog [418e85d] git push origin HEAD:master --force
```

此时我们可以通过下述命令进行解决

```bash
git checkout master
git merge 418e85d
git push origin HEAD:master --force
```