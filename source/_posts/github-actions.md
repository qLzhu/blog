---
title: Github Actions CI 工作流程
abbrlink: 40311
date: 2020-04-19 10:44:30
categories: github
tags:
  - actions
  - github actions
  - github-pages-deploy-action
  - not a recognized Liquid tag
---

[Github Actions](https://help.github.com/cn/actions) 是 Github 在2018年10月推出的持续集成 (Con­tin­u­ous in­te­gra­tion，简称 CI) 服务，2019年11月正式开放。Github Actions 提供了配置非常不错的虚拟服务器环境，基于它可以进行构建、测试、打包、部署项目。Github Actions 的另一个好处就是 Github 为它专门在市场开辟了专栏，如果你需要某个 Action 的话，完全没必要自己写复杂的脚本，直接引用它人的脚本即可<span class="text-gray">（推荐到 [Github市场](https://github.com/marketplace?type=actions)、[Github官方Action脚本](https://github.com/actions)、[Awesome Actions](https://github.com/sdras/awesome-actions) 内查找需要的Action）</span>

{% note %}
Github Actions术语：
1. Workflow 工作流程 <span class="text-gray">（持续集成一次运行的整个流程）</span>
2. Job 任务 <span class="text-gray">（1~N个Job组成Workflow）</span>
<span class="text-gray">注意部署的虚拟服务器系统，会影响到Jobs同时触发的个数。免费版部署macos系统最多能触发5个、其它系统则最多能触发20个。详情请查阅 https://help.github.com/cn/actions/reference/workflow-syntax-for-github-actions#usage-limits</span>
3. Step 步骤 <span class="text-gray">（1~N个Step组成Job）</span>
4. Action 动作\命令 <span class="text-gray">（1~N个Action组成Step）</span>
5. Workflow File 配置文件 yaml 格式，需要放置在项目根目录下 .github/workflow/ 内
{% endnote %}
<!-- more -->

## 部署hexo实践

### 创建项目

首先在 Github 上创建个 [github-actions-test](https://github.com/qLzhu/github-actions-test) 空白仓库，然后再在本地创建个同名的仓库，并跟远程仓库关联起来

```bash
mkdir github-actions-test && cd $_
hexo init && git init

git add .
git commit -m "docs(new):initial"

# 建立 gh-pages 静态网站分支
git branch gh-pages

# 关联远程仓库并提交
# 注意：要把 username 换成你的 GitHub 用户名
git remote add origin git@github.com:username/github-actions-test.git
git push -u origin master
git push -u origin gh-pages
```

配置hexo _config.yml

```yml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
# username 换成你的 GitHub 用户名
url: https://username.github.io/github-actions-test
root: /github-actions-test/

# Include / Exclude file(s)
## include:/exclude: options only apply to the 'source/' folder
include:
  - .nojekyll
```

再在 source/ 目录下创建空白文件 .nojekyll

```bash
cd source/
touch .nojekyll
```

配置hexo _config.yml 和创建 .nojekyll 是为了绕过 GitHub Pages 上的Jekyll处理，不然 GitHub Pages 会报 `The tag fancybox on line 77 in themes/landscape/README.md is not a recognized Liquid tag.` 错误
https://github.blog/2009-12-29-bypassing-jekyll-on-github-pages/
https://github.com/theme-next/hexo-theme-next/issues/410

### 设置 Token

在 Github 上 **Settings -> Developer settings -> Personal access tokens -> Generate new token** 处创建一个新的 token，该 token 只开启一个 repo 权限即可

> `Note` 的名称随意，自己看到知道是什么token就没问题
> 实在不知道如何创建token的请查阅 https://help.github.com/cn/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line

{% asset_img Set_Github_Tokens.png 设置 token %}

设置好后，滚动到界面最下方，点击 Generate token 按钮进行创建。注意：创建完的 token 只有第一次可见，之后再访问皆无法再见<span class="text-gray">（只能看见 token 的名称）</span>，想查看只能重新再创建，因此要保存好这个值

{% asset_img Token_hash.png token 值 %}

### 设置密钥

把刚才生成的 token 值保存到当前仓库的 `Settings/Secrets` 里面。Name 的名称随意，建议名称全部大写并加下划线的形式，Workflow File 配置文件内会用到该名称

{% asset_img GH_ACTION_TEST_KEY.png token保存到当前项目 %}

## 配置 Workflow File

在项目的根目录下，创建 `.github/workflows/deploy.yml` 文件，并键入下述内容

{% include_code .github/workflows/deploy.yml lang:yml workflow-file.yml %}

编辑完成后 push 到远程仓库，紧接着 Actions 就会显示构建界面

{% asset_img Successful.png Actions部署成功界面 %}

你还可以把 build passing 图标放置在你想展示的地方

{% asset_img Build_Passing.png build passing 图标 %}

{% note %}
注意Github Actions使用限制：
1. 每个仓库只能同时支持20个Workflow并行，每个Workflow最多允许运行72小时，超时会自动取消该工作流
2. 每小时可以调用1000次GitHub API
3. 每个Job最多可以执行6个小时，超过该时间会自动终止Job
4. 私有仓库每月累计使用时间为2000分钟，超过后$0.008/分钟，公共仓库则无限制

虽然名称叫持续集成，但当所有任务终止和完成时，虚拟环境内的数据会随之清空，并不会持续。即每个新任务都是一个全新的虚拟环境。
{% endnote %}
