---
title: 设置 GIT 默认编辑器
abbrlink: 5731
date: 2021-10-06 19:53:14
categories: git
tags: [git, git config --global core.editor "vim"]
---

当我 `git commit --amend` 修改完附注后，发现 `:wq` 没法退出，报

```bash
E45: 'readonly' option is set (add ! to override)
```

原因是没有修改 GIT 的默认编辑器

执行下述命令即可解决

```bash
git config --global core.editor "vim"
```

CONFIG 更多命令请参考 [git config](https://www.atlassian.com/git/tutorials/setting-up-a-repository/git-config)