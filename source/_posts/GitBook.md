---
title: GitBook
categories: gitbook
tags:
  - gitbook
  - gitbook init
abbrlink: 19654
date: 2020-05-02 13:02:25
---

[GitBook](https://github.com/GitbookIO/gitbook)是一个使用GIT、Github和Markdown语法来构建书籍的工具，并且能够将你构建出来的书籍输出为pdf、epub、mobi等格式。它可以让程序猿像编代码一样编辑自己的书籍或者笔记，并且托管服务是完全免费的跟Github一样

## Install

GitBook依赖Node环境，所以需要先安装Node再全局安装GitBook，Node安装请查阅{%post_link mac-uninstall-app%}

```bash
npm install -g gitbook-cli
```
<!-- more -->

## Init

创建本地书籍目录并使用GitBook初始化

```bash
mkdir gitbook-example && cd $_ && gitbook init
```

初始化完成后，GitBook会在该目录下创建两个文件README.md和SUMMARY.md

```bash
tree ./

./
├── README.md  #书籍的序或者前言
└── SUMMARY.md #目录，GitBook会按照这个文件来生成书籍的结构，目录深度只支持两级
```

编辑SUMMARY.md书籍目录，就跟我们平时在MD文件里添加链接是一样的。方括号{%label default@[]%}内的是章节名称，小括号{%label default@()%}内的则是章节路径。例如：

```markdown ./SUMMARY.md
# 目录
# 这里的标题并不会显示，注释作用
# 下方的 --- 会呈现一个分隔线

- [概要](README.md)
- [第一章 什么是Ajax](lesson01/what.md)
    + [1.1 诠释Ajax](lesson01/1.1.md)
        * [1.1.1 关键之处](lesson01/1.1.1.md)
        * [1.1.2 种种选择](lesson01/1.1.1.md)
    + [1.2 Ajax工具包](lesson01/1.2.md)
    + [1.3 小结](lesson01/1.3.md)
---
- [第二章 JavaScript 和 DOM](lesson02/dom.md)
```

上述内容添加到SUMMARY.md后，执行`gitbook serve`即可预览 http://localhost:4000/

{% asset_img gitbook-serve-page.png %}

- `-o, --output <directory>` 指定构建的静态文件输出目录，默认 ./_book
- `-f, --format <name>` 指定输出格式
- `--config <config file>` 指定配置文件，默认为 ./book.js 或 ./book.json

注意：要想把侧边栏导航的“Introduction”更改成中文语言，需要按照上述写法，把README.md链接到无序列表上。其它写法或者不写，README.md默认都会指向“Introduction”

## Gitbook commands

```bash
gitbook help                   #列出所有指令
gitbook --help                 #列出gitbook-cli的指令
gitbook --version              #查看版本
gitbook update                 #更新
gitbook init                   #初始化
gitbook serve                  #生成静态网页并运行服务器
gitbook serve -p 8000
gitbook build                  #生成静态网页(点击index.html可直接使用的，默认生成到 ./_book)
gitbook build ./ ./docs        #使用github pages时，静态文件生成到 ./docs
gitbook ls                     #列出本地所有的gitbook版本
gitbook current                #列出当前使用的版本
gitbook ls-remote              #列出远程所有的gitbook版本
gitbook fetch 1.0.0            #安装指定版本
gitbook build --gitbook=1.0.0  #指定gitbook版本生成静态网页, 本地没有会先下载
gitbook uninstall 1.0.0        #卸载gitbook版本
gitbook install                #安装插件也可使用npm
gitbook build --log=debug      #指定log的级别
gitbook pdf                    #生成pdf文件，需要单独下载 ebook-convert https://gist.github.com/AaronO/9962667
gitbook epub
gitbook mobi
```

## Style

在项目根目录下创建book.json，还可以针对特性格式用css设置样式

```json ./book.json
"styles": {
  "website": "",
  "ebook": "",
  "pdf": "",
  "mobi": "",
  "epub": ""
}
```

例如隐藏侧边栏的Published with GitBook字样

```css ./style/website.css
.gitbook-link {
  display: none;
}
```

## Cover

设置书籍的封面，可让书籍看上去更加正规及其优雅。封面图片的格式为JPEG或PNG。也可使用 [gitbook-plugin-autocover](https://www.npmjs.com/package/gitbook-plugin-autocover) 插件生成封面

- cover.jpg：1800x2360px
- cover_small.jpg：200x262px

封面遵循

- 没有边框
- 清晰可见的书籍标题
- 任何重要的文字在小版本（cover_small）中都应该是可见的

## Glossary

在GLOSSARY.md可以设置特定的词汇，鼠标移到GitBook任意文章的特定词汇上，会提示词汇的示意，类似HTML的alt效果

```markdown ./GLOSSARY.md
## Git
分散式版本控制软件
```

## Langs

GitBook支持使用多语言来构建书籍。需要在项目的根目录下创建LANGS.md文件，并为每种语言创建一个子目录，该子目录下必须含有README.md和SUMMARY.md。例如：https://github.com/GitbookIO/git

```markdown ./LANGS.md
* [英语](en/)
* [法语](fr/)
* [西班牙语](es/)
```

如果只是设置中文简体语言，只需要在book.json内添加`"language": "zh-hans"`即可

## Ignore

设置需要忽略的文件/文件夹，GitBook会自动读取根目录下的`.gitignore`、`.bookignore`和`.ignore`

```bash ./.gitignore
.DS_Store
_book/
node_modules/
```

## Push

本文以集成Github为例，演示Gitbook如何发布电子书籍

1. 在Github上创建开源仓库gitbook-example{%label info@（如何创建此处不赘述）%}
  1.1. `git init`
  1.2. `git remote add origin <git......>`
  1.3. `git add .`
  1.4. `git commit "docs(add):initial"`
  1.4. `git push --set-upstream origin master`
2. 注册Gitbook{%label info@（注册比较简单就不截图了）%}，创建书籍项目（名称可不一样）并同步gitbook-example仓库的master分支

{% asset_img create.png %}
{% asset_img create-example.png %}
{% asset_img github.png %}
{% asset_img link-github.png %}
{% asset_img github-select-item.png %}
{% asset_img github-select-branch.png %}

设置完成以后，再push到Github上，Gitbook内的书籍就会自动拉取更新。分享出去或者访问该书籍，请到侧边栏的 share 内查看

{% asset_img gitbook-share.png %}

除了在Gitbook显示静态网页外，我们还可以开启Github Pages，开启的前提是，使用Gitbook build把静态文件发布到根目录下的docs内，然后再到gitbook-example仓库下开启Github Pages

1. 本地项目下执行 `gitbook build ./ ./docs`
2. `git add .`
3. `git commit -m "docs(add):docs folder"`
4. `git push`
5. gitbook-example仓库下选择 Settings -> Github Pages -> Source -> master branch/docs folder 开启
6. 浏览器打开 https://your_name.github.io/gitbook-example/ ，就可以访问你的书籍了

{% asset_img github-pages.png %}

## Book.json

```bash
{
  # JSON不支持注释，使用时请删除
  # 常规设置
  # =======================================
  # 设置根目录地址
  "root": "./",
  # 输出文件夹
  "output": null,
  # 输出的书籍类型
  "generator": "site",
  # ebook格式 "epub", "pdf", "mobi"
  "extension": null,
  # 书籍标题
  "title": "GitBook Example",
  # 作者
  "author": "qinlzhu",
  # 书籍描述
  "description": "This is GitBook Example",
  # 书籍语言
  "language": "zh-hans",
  # 书籍的国际标准书号
  # https://isbnsearch.org/
  "isbn": "978-0-13-601970-1",
  # 书籍版本
  # https://semver.org/lang/zh-CN/
  "gitbook": "1.0.0",
  # 书籍各格式自定义的样式
  "styles": {
    "website": "./styles/website.css"
  },
  # 目录结构配置
  # 指定README.md,SUMMARY.md,GLOSSARY.md和LANGS.md文件名称
  "structure": {
    "readme": "README.md",
    "summary": ""
  },
  # 侧边栏链接
  "links": {
    "sidebar": {
      "blog": "https://qlzhu.github.io/blog"
    }
  },
  # 插件列表
  # https://www.npmjs.com/search?q=gitbook-plugin
  "plugins": [
    # 去除自带的插件
    "-search",
    "github"
  ],
  # 各插件配置列表
  "pluginsConfig": {
    "github": {
      "url": "https://qlzhu.github.io/blog"
    }
  },
  # PDF配置
  "pdf": {
    "toc": true,
    "pageNumbers": false,
    "fontSize": 12,
    "paperSize": "a4",
    "margin": {
      "right": 62,
      "left": 62,
      "top": 36,
      "bottom": 36
    }
  }
}
```
