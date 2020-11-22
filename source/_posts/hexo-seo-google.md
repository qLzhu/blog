---
title: SEO Google 搜索引擎收录设置
abbrlink: 10730
date: 2020-05-09 13:08:10
categories: hexo
tags:
  - hexo
  - sitemap
  - google_site_verification
---

博客只是单纯的部署到 Github Pages，各大搜索引擎是不会主动收录博客内容的，需要我们手动提交并设置。2015年3月26日，中国内网和互联网边界的某个设备劫持了进入中国的 HTTP 连接，将一些来自百度的 JavaScript 文件替换为恶意文件，DDoS(distributed denial-of-service) 攻击了 Github Pages 上的 GreatFire 和纽约时报镜像。Github 从此屏蔽了百度，而且这个屏蔽措施至今都没有解除，所以此文不会赘述百度等搜索引擎的设置方案

> https://www.githubstatus.com/
> 科学上网请查阅 {% post_link vpn-shadowsocks-outline %}
> 百度的兼容方案请查阅 [hexo-baidu-url-submit](https://github.com/huiwang/hexo-baidu-url-submit) 和 [hexo-generator-baidu-sitemap](https://github.com/coneycode/hexo-generator-baidu-sitemap)

## Install

安装 [hexo-generator-sitemap](https://github.com/hexojs/hexo-generator-sitemap) 插件
> 执行 hexo g 时会自动在 public 下生成 sitemap.xml

```bash
npm install hexo-generator-sitemap
```
<!-- more -->

在站点配置文件中设置 sitemap.xml 的路径，作用就是为搜索引擎指路，优化搜索结果，让你的网站出现在搜索结果中的地图，所以站点地图对网站非常重要

```yml ./_config.yml
sitemap:
  path: sitemap.xml
```

## reobots.txt

[robots](https://ziyuan.baidu.com/college/courseinfo?id=267&page=12#h2_article_title28 "百度官方解释") 是站点与蜘蛛 spider 沟通的重要渠道，站点通过 robots 文件声明不想被搜索引擎收录的部分或指定搜索引擎只收录特定的部分

- Allow    可以抓取的部分
- Disallow 禁止抓取的部分
- Sitemap  指定sitemap.xml的位置{%label info@（your_name注意修改成你Github的用户名）%}

```txt ./source/robots.txt
User-agent: *
Allow: /
Allow: /archives/
Allow: /tags/
Allow: /categories/
Allow: /about/
Allow: /404/

Disallow: /js/
Disallow: /css/
Disallow: /fonts/
Disallow: /vendor/
Disallow: /styles/

Sitemap: https://your_name.github.io/blog/sitemap.xml
```

## Google Search Console

打开 [Google Search Console](https://search.google.com/search-console/about?hl=zh-CN&utm_source=wmx&utm_medium=wmx-welcome) 添加站点网址，并根据提示完成验证

{% asset_img google-verification.png %}

选择 “其他验证方法” - “HTML 标记” 将会获取到一段 META 标签

```html
<meta name="google-site-verification" content="59As2-XGZdaAcxxxxxxxxxxxxxxx" />
```
{% asset_img google-verification-code.png %}

将 content 属性的值 59As2-XGZdaAcxxxxxxxxxxxxxxx 追加到 ./source/_data/next.yml 底部保存，然后把所有更改 Push 到 Github，再去完成 Google Search Console 的验证

> 只 Push 到 Github，是因为博客使用的是 Travis CI 自动部署的，所以不需要执行 `hexo deploy`
> 如何设置自动化部署请查阅 {% post_link hexo-Travis-CI-自动化部署静态博客 %} 和 {% post_link github-actions %}

```yml ./source/_data/next.yml
google_site_verification: 59As2-XGZdaAcxxxxxxxxxxxxxxx
```

{% asset_img google-verification-succeed.png %}

验证成功后进到后台，点击左侧站点地图，输入 sitemap.xml 确认提交，至此 Google 应该以收录你的站点了！

{% asset_img google-verification-sitemap.png %}

检测站点是否被收录，可以使用 Google Search Console 的网址检测，还可以使用 Google 的搜索引擎搜索 {%label info@site:<域名>%} 来查看个人站点是否已被搜索引擎收录

{% asset_img google-verification-ispost.png %}
{% asset_img google-search.png %}

Google 抓取网页需要一定时间，站点地图提交后可能不会立即编入索引。如果验证结果没有收录，可以在几分钟后再次验证。

至此，你的个人网站已被 Google 收录。如果你对站点地图文件做出了更改，Google 会在下次抓取你的网站时发现你的更改，所以不必重新提交已知的站点地图

## Google Analytics

想了解更为详细的站点访问数，访问时间，用户分布等信息，可以使用 [Google Analytics](https://analytics.google.com/) ，它可以有效的帮助你掌握网站情况，更好地管理网站

1. 注册 [Google Analytics](https://analytics.google.com/) 并根据提示填写相关信息
2. 获取账户的跟踪 ID 并部署到站点上

{% asset_img google-analytics-setting.png %}
{% asset_img google-analytics-id.png %}

```yml ./source/_data/next.yml
google_analytics:
  tracking_id: UA-XXXXXXXXXX
  only_pageview: true
```

3. Push 到 Github
4. 使用 [Google Analytics](https://analytics.google.com/) 流量检测实时流量报告（就是让你试下准不准确、行不行）

{% asset_img google-analytics-zhui.png %}
{% asset_img google-analytics-info.png %}
{% asset_img google-analytics.png %}
