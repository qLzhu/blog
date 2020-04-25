---
title: Mac启动WEB服务的方法
abbrlink: 3450
date: 2020-04-22 16:21:31
categories: mac
tags:
  - mac
  - web server
---

MAC默认集成了Python2<span class="text-gray">（使用python3需要升级）</span>和PHP的开发环境，所以启动WEB服务时无需单独安装。命令方式进到要开启服务的目录，再使用对应的命令开启。访问时在浏览器地址栏中输入 https://localhost:8000 ，共享给同事时localhost改成你的IP即可

Python方式启动WEB服务

```bash
python -m SimpleHTTPServer 8000
python3 -m http.server --cgi 8000
```

PHP

```
php -S localhost:8000
```

Serve需要使用NPM安装

```
npm i -g serve

# 启动 web 服务命令，无需写端口号
# 默认 5000 端口
serve
```
