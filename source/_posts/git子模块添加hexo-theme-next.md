---
title: Git Submodule
categories: git
tags:
  - NexT
  - git submodule add <url> themes/next
abbrlink: 46430
date: 2020-04-16 19:09:02
---

博客重构执行 `git add` 时报如下错误

```
$ git add .

warning: adding embedded git repository: themes/next
hint: You've added another git repository inside your current repository.
hint: Clones of the outer repository will not contain the contents of
hint: the embedded repository and will not know how to obtain it.
hint: If you meant to add a submodule, use:
hint:
hint:   git submodule add <url> themes/next
hint:
hint: If you added this path by mistake, you can remove it from the
hint: index with:
hint:
hint:   git rm --cached themes/next
hint:
hint: See "git help submodule" for more information.
```
<!-- more -->

这是因为 git 检测到 themes/next 目录是来自另一个 git 项目，不能直接作为本项目的文件进行上传

解决办法如下：
1. 删除 themes/next/.git 目录
2. 把 hexo-theme-next 添加到 git 子模块

我们采用第二种方式进行解决。先使用 `git rm -r --cached themes/next` 清除下暂存区和储存区中的 NexT 记录

再使用 `git submodule` 命令把 hexo-theme-next 添加到 git 子模块

```git
git submodule add https://github.com/theme-next/hexo-theme-next themes/next
```

执行后，git 会在根目录下生成一个 `.gitmodules` 文件。运行 `cat .gitmodules`，我们应该看到这样的东西

```git
[submodule "themes/next"]
  path = themes/next
  url = https://github.com/theme-next/hexo-theme-next
```

至此再添加或者提交的话，就没问题啦

如果你想把 hexo-theme-next 添加到 git 子模块，单独提交一个 commit 的话，可先把 themes/next 排除
```git
git add .
git reset -- themes/
```

注意：以后更新 hexo-theme-next 子模块的话，需要使用 `git submodule update` 命令。以下列几个常用的命令，更多请查阅 GIT-SCM 官网 https://git-scm.com/docs/git-submodule

```bash
git submodule                                             #查看子模块
git submodule add ["remote repo"]                         #添加子模块
git submodule add ["remote repo"] ["path\submodule name"]
git submodule init                                        #初始化本地配置文件
git submodule update --remote                             #更新子模块为远程项目的最新版本
git submodule update ["path\submodule name"]              #更新子模块的指定路径
git clone --recursive ["remote repo"]                     #克隆包含子模块的仓库
git rm ["path\submodule name"]                            #删除子模块
git submodule foreach ["command"]                         #遍历子模块执行同样的命令
```

如果本地已存在 .gitsubmodule 文件，请执行下述命令将子模块下载下来

```bash
git submodule update --init --recursive
```
