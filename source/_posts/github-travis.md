---
title: Github Action 部署 blog
categories: hexo
tags:
  - github
  - travis
  - token
abbrlink: 19620
date: 2021-10-03 14:48:19
---

之前使用的免费版 [Travis CI](https://app.travis-ci.com/) 由于有时间限制，所以，本次把自动部署更改为 [Github Action](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions) 进行部署。

## 创建 Github Token

在 Github 上 {%label @Settings -> Developer settings -> Personal access tokens -> Generate new token 处创建一个新的 token %}

- Note：Token的名称随便定义
- Expiration：Token的有效期，我设置的长时间即No expiration
- Select scopes：一定要勾选 repo、workflow 两个选项
<!-- more -->

{% asset_img CreateToken.jpg 创建新的Token %}

创建成功后该 Token 值只显示一次
所以，我们需要先保存好该值，否则刷新返回界面后无法再找回

{% asset_img accesstoken.jpg Token值 %}

## 设置项目 Token

到 {%label @Github项目下的 -> settings -> Secrets -> New repository secret %} 处粘贴刚才创建的 Token

{% asset_img saveToken.jpg Token保存在到项目下 %}

## 在项目下创建 workflow 文件

Github Action 使用的是 YAML 语法来定义的，执行文件需要保存到项目根目录下的 .github/workflows 内

这里我们创建一个名称叫 hexo-deploy.yml 的可执行文件
执行文件名称可随便定义

```bash
mkdir .github/workflows && cd $_
touch hexo-deploy.yml
```

我们使用编辑器打开该文件（也可直接在编辑器内直接创建目录及其文件 随意 怎么方便怎么来）

```yml
# 项目名称，随意定义
name: deploying Hexo project to GitHub pages

# 设定 master 分支 push 时可触发下述事件
on:
  push:
    branches:
      - master

# 事件
jobs:
  # 任务名称，可随便定义
  pages:
    # 定义执行的虚拟环境
    runs-on: ubuntu-latest
    # 事件步骤固定格式
    steps:
      # 获取 blog 源码及其主题
      # uses 使用的是哪个 Action 插件
      - uses: actions/checkout@v2
        # with 下是该插件的参数
        # 我们使用的是 Next 主题下面的设置至关重要，否则会报错
        with:
          submodules: true
          fetch-depth: 0
      # 设置 node 执行环境
      - name: Use Node.js 12.x
        uses: actions/setup-node@v1
        with:
          node-version: '12.x'
      # 设置缓存以此加快工作流
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      # 安装依赖
      - name: Install Dependencies
        run: npm install -g npm && npm install
      # hexo 生成静态文件
      - name: Build
        run: hexo generate
      # 部署静态文件到 gh-pages 分支
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          # 注意
          # 注意
          # 这里的 BLOG_TOKEN 名称要与我们在第二个步骤时设定的名称保持一致
          github_token: ${{ secrets.BLOG_TOKEN }}
          publish_dir: ./public
          publish_branch: gh-pages
```

至此 Github Action 设置完毕，我们正常使用 git 提交文件即可，剩余静态文件的部署会有 Action 帮我们完成！

{% asset_img ok.jpg Action部署成功界面 %}

## 拓展阅读

- [Github Action](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
- [actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)