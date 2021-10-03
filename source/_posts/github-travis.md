---
title: 更改 Travis 长期 Token
categories: hexo
tags:
  - github
  - travis
  - token
abbrlink: 19620
date: 2021-10-03 14:48:19
---

本 blog 用的 Github + [Travis](https://app.travis-ci.com/) 方式进行部署的，之前的 Token 有时间限制，由于长时间未管理及其更新，造成了权限丢失。所以，我们此次更换 Travis 项目下的 Token 值。

Travis基础知识
{% cq %}
{% post_link hexo-Travis-CI-自动化部署静态博客 Travis自动化部署 %}
{% endcq %}

## 创建 Token

在 Github 上 **Settings -> Developer settings -> Personal access tokens -> Generate new token** 处创建一个新的 token，重点是 __Expiration * 处，要设置成 No expiration__，该 token 只开启一个 repo 权限即可。
<!-- more -->

{% asset_img expiration.jpg 设置 token %}

设置好后，滚动到界面最下方，点击 Generate token 按钮进行创建。注意：创建完的 token 只有第一次可见，之后再访问皆无法再见（<span class="text-gray">只能看见 token 的名称</span>），想查看只能重新再创建，因此要保存好这个值。

{% asset_img accesstoken.jpg token值 %}

## 部署到Travis

得到长期的 token 后，我们需要回到 [Travis](https://app.travis-ci.com/) 官网，把之前项目下的 token 值更换掉。

{% asset_img blog.jpg token值 %}
{% asset_img reset.jpg token值 %}