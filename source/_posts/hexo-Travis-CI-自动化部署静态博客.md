---
title: Travis CI 自动化部署静态博客
categories: hexo
tags:
  - hexo
  - Travis CI
  - github pages
date: 2020-04-16 20:32:48
abbrlink: 65246
---

## 新建项目

登陆 [GitHub](https://github.com/) 创建一个空的开源项目 trcl（<span style="color: #999;">项目名称随意，初次测试 Travis CI 最好跟着本文逐步操作，了解后再自己测试</span>）

{% asset_img New_Repository.png Github上新建项目 %}
<!-- more -->

项目新建后，再使用如下命令初始化本地项目（<span style="color: #999;">不能直接 `Git Clone` 到本地再初始化，因为 `hexo init` 的必须是空白项目</span>）

```bash
mkdir trcl && cd $_
hexo init && git init
```

和远程 origin（<span style="color: #999;">克隆版本库的时候，所使用的远程主机自动被Git命名为origin</span> ）建立链接关系

```bash
git remote add origin git@github.com:***/trcl.git
```

landscape 主题更改为 NexT 主题，并提交到远程仓库

```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
rm -rf themes/landscape/
# NexT主题设置为Git子模块命令
git submodule add https://github.com/theme-next/hexo-theme-next themes/next

git add .
git commit -m "docs:init blog"
git push -u origin master
```

新建 gh-pages 分支并提交，以此用来部署静态网站
```bash
git branch gh-pages
git push -u origin gh-pages
```

## 配置 Hexo _config.yml

把 gh-pages 分支同步到远程分支后，Github 会直接把该分支作为默认的静态网站进行部署。网址格式会按照这个格式进行生成 `https://username.github.io/trcl`，把 username 更换成你 Github 的用户名填写到下面（<span style="color: #999">也可以到 Github 上 Settings -> GitHub Pages 处查看</span>）

```yml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://yourname.github.io/trcl
root: /trcl/
```

## 设置 Tokens

在 Github 上 Settings -> Developer settings -> Personal access tokens -> Generate new token 处创建一个新的 token，该 token 只开启一个 repo 权限即可

> `Note` 的名称随意，自己看到知道是什么token就没问题

{% asset_img Set_Github_Tokens.png 设置 token %}

设置好后，滚动到界面最下方，点击 Generate token 按钮进行创建。注意：创建完的 token 只有第一次可见，之后再访问皆无法再见（<span style="color: #999">只能看见 token 的名称</span>），想查看只能重新再创建，因此要保存好这个值

{% asset_img Token_hash.png token 值 %}

## 配置 Travis CI

登陆或注册 [Travis CI](https://travis-ci.com/) 需要使用 GitHub 账户，成功后应该是如下界面。到该界面后选择以下任意一个按钮点击（<span style="color: #999">+或者Activate all repositories using github apps</span>），进行 GitHub 项目的选择进行部署 Travis CI

{% asset_img Travis_CI_Sign_In.png 注册Travis CI %}
{% asset_img Select_Trcl.png 部署Travis CI %}

Travis CI 部署到我们 trcl 项目后，我们还需要在 Travis CI 里把上一步创建的 token 值，配置到项目内

{% asset_img Select_Trcl_Successful.png 设置token %}
{% asset_img Set_Trcl_Token.png 添加token界面 %}

{% note %}
说明：
“NAME” 名称随便定义
“VALUE” 填写上一步创建的 token 值
“BRANCH” 选择要监测的分支
“DISPLAY VALUE IN BUILD LOG” 是 token 值是否显示在日记的意思，不用开启
{% endnote %}

## 设置 .travis.yml

上述步骤全部完成后，我们就需要设置 Travis CI 的配置文件 .travis.yml 啦

{% include_code .travis.yml lang:yml travis.yml %}

按照如上配置设置完成后，分别执行下`git add`、`git commit` 和 `git push` 提交下

此时你再回到 Travis CI 应该可以看到构建成功的界面或者正在构建的界面（<span style="color: #999">如果构建失败，解决问题后重新提交 commit，再点击 Restart build 重新执行即可</span>）

{% asset_img Travis_CI_Current.png 构建成功界面 %}

另外点击 build passing 图标，还能够获得其链接，可以将它贴在其他地方，随时监控博客的 build 状态

## 删除 Travis CI 项目

{% asset_img Delete.png Travis CI 项目管理界面 %}
{% asset_img Delete_item.png 删除界面 %}

- 单个项目时，直接在 Github 上删除仓库即可
- 多个项目时，按照上述步骤删除部署 Travis CI 的项目

## 阅读拓展

- [Hexo 官网](https://hexo.io/zh-cn/index.html)
- [Travis CI 官网](https://travis-ci.com/)
- [GitHub Pages Deployment](https://docs.travis-ci.com/user/deployment/pages/)
- [阮一峰：持续集成服务 Travis CI 教程](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)
- [G2ex：使用 Travis 自动化部署 Hexo Blog](https://g2ex.top/2019/06/28/hexo-with-travis-ci/)
- [iissnan：使用 Travis CI 自动更新 GitHub Pages](https://notes.iissnan.com/2016/publishing-github-pages-with-travis-ci/)
- [Hexo + GitHub Pages + Travis CI 自动化部署静态博客](https://blog.i1hao.com/2018/09/01/hexo-and-githubpages-best-practices/)
- [警惕 Travis CI 的 npm 缓存](http://claude-ray.com/2019/08/01/travis-npm-cache/)