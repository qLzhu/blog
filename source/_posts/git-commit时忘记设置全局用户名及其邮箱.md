---
title: Commit 未设置 Author 和 Email
abbrlink: 58535
date: 2021-10-02 16:25:00
categories: git
tags: [git, git commit, git commit --amend --author]
---

由于电脑更新，期间重新装过 Git 所以造成了全局下的用户名及其邮箱丢失，在 commit 时才知道，这时我们可通过下述命令对上次的 commit 附注进行设置，在命令的后方加 --no-edit 参数不会打开编辑器。

```bash
git commit --amend --author="Author Name <email@address.com>" --no-edit
```

全局设置

```bash
git config --global user.name ["Author Name"]
git config --global user.email ["email@address.com"]
```