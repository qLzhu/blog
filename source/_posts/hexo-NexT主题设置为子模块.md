---
title: git子模块添加hexo-theme-next
categories: git
tags:
  - NexT
  - git submodule add <url> themes/next
abbrlink: 46430
date: 2020-04-16 19:09:02
---

博客重构 `git add` 添加到暂存区时报如下错误

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

注意：以后更新 hexo-theme-next 子模块的话，需要使用 `git submodule update` 命令

## 阅读拓展
- [Git工具-子模块](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)
- [Hexo文章发布环境的自动部署02-Hexo配置](https://www.charlesjoe.com.cn/category/20190525-hexo-autodeploy-two.html)
- [如何使用Git子模块迁移和同步Hexo和theme-next](http://blog.zedyeung.com/2018/08/05/How-to-migrate-and-synchronize-Hexo-and-theme-next-with-git-submodules/)