---
title: 部署博客到 Github Pages
categories: hexo
tags:
  - hexo
  - github pages
  - hexo-deployer-git
abbrlink: 51941
date: 2020-04-15 17:26:48
---

[GitHub](https://github.com/) 是通过 Git 进行版本控制的软件源代码托管服务平台，它有很多人性化的功能可以帮助我们管理和追踪代码，把博客部署到 Github 上，最主要的原因就是用它的 [Github Pages](https://pages.github.com/) 服务，它可以为我们提供免费的静态网站托管服务，用起来方便而且功能强大，不仅没有空间限制，还可以绑定自己的域名

这里我们不讲如何通过 Github 创建静态网站，只讲如何把本地博客部署到 GitHub 上！
<!-- more -->

部署环境及其账户：
- [注册好的 Github 账户](https://github.com/)
- [Node.js](http://nodejs.cn/download/)
- [Git](https://git-scm.com/downloads)
- [Hexo](https://hexo.io/zh-cn/)

```bash
# 全局安装 hexo
npm install hexo-cli -g

# 打开 Terminal 使用命令，在本地创建一个博客目录并进到该目录下
mkdir blog && cd $_

# Hexo 和 git 初始化该目录
hexo init && git init

# 链接远程仓库（ssh）
# 需要先创建个空白的公开仓库，注意创建时不要选择自动创建 README.md 和 license 等文件
git remote add origin git@github.com:xxx/blog.git

# 安装部署到 Github 上时所使用的插件
npm install hexo-deployer-git --save

# 提交
git add .
git commit -m "xxx"
git push -u origin master

# 创建 gh-pages 分支
# master 分支用作保存源码使用，而 gh-page 则是部署静态代码使用
# 提交后 Github 会默认 gh-pages 为静态网站分支，无须再开启，默认链接是 https://username.github.io/blog
git branch gh-pages
git push -u origin gh-pages
```

配置 hexo 的 _config.yml 文件
```yml
deploy:
  - type: git
  # 自定义提交信息，可不配置
  message: 博客更新时间{{ now('YYYY-MM-DD HH:mm:ss') }}
  # Github 的 ssh 地址及其要部署到哪个分支
  repo:
    github: git@github.com:xxx/blog.git,gh-pages
```

配置完成后，随便书写点内容，执行 `hexo g -d`，再去 Github 上的仓库内看看 gh-pages 分支是不是已经变成静态内容了

**原理：**执行 `hexo g -d` 操作时会在本地生成 `public` 静态代码和 `.deploy_git` 文件夹。`.deploy_git` 和 `public` 的内容几乎一致，但 `.deploy_git` 多了 GitHub 所需的仓库信息与提交信息。**全部解析完后 hexo 会把 `.deploy_git` 文件夹内的全部内容推送到 GitHub 仓库中，再由 Github Pages 服务完成静态网站的解析**
