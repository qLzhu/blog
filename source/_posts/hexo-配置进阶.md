---
title: Hexo 配置进阶
abbrlink: 49139
date: 2020-04-15 23:37:21
categories: hexo
tags:
  - hexo
  - NexT
  - hexo-abbrlink
  - hexo-generator-searchdb
  - hexo-symbols-count-time
---

本博客采用的是 NexT 主题，以下配置结合 hexo 和 NexT 两方面混合讲解。说明：NexT 7.3 配置文件及其目录也发生了很多变化，并且支持了 Pjax。使得我们可以通过启动数据目录来管理配置，升级新的 release 版本更加平滑

- [NexT 数据目录官方说明](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/DATA-FILES.md)
- [Hexo-NexT 版本更新记录](https://tding.top/archives/2bd6d82.html)
- [NexT 7.3 数据目录及自定义 CSS 的启用方式](https://www.imczw.com/post/tech/next_data_file.html)
<!-- more -->

## 语法转义

特殊符号 &#123;&#123;&#125;&#125; 在 Hexo 内是有特殊含义的语法，在文章中你不能直接使用，需要[转义字符](https://www.sojson.com/unicode.html)来代替

``` bash
! &#33;         — 惊叹号 Exclamation mark
” &#34; &quot;  — 双引号 Quotation mark
# &#35;         — 数字标志 Number sign
$ &#36;         — 美元标志 Dollar sign
% &#37;         — 百分号 Percent sign
& &#38; &amp;   — And/Ampersand
‘ &#39;         — 单引号 Apostrophe
( &#40;         — 小括号左边部分 Left parenthesis
) &#41;         — 小括号右边部分 Right parenthesis
* &#42;         — 星号 Asterisk
+ &#43;         — 加号 Plus sign
< &#60; &lt;    — 小于号 Less than
= &#61;         — 等于符号 Equals sign
- &#45; &minus; — 减号
> &#62; &gt;    — 大于号 Greater than
? &#63;         — 问号 Question mark
@ &#64;         — At
[ &#91;         — 中括号左边部分 Left square bracket
\ &#92;         — 反斜杠 Reverse solidus (backslash)
] &#93;         — 中括号右边部分 Right square bracket
` &#96;         - 重音号 backquote/grave accent
{ &#123;        — 大括号左边部分 Left curly brace
| &#124;        — 竖线Vertical bar
} &#125;        — 大括号右边部分 Right curly brace
```

## 新建页面

```bash
hexo new page tags
```

```yml 配置 source/_data/next.tml
# ---------------------------------------------------------------
# Menu Settings
# ---------------------------------------------------------------
menu:
  home: / || fa fa-home

  # 开启 tags 和 categories 需要在 source/**/index.md 内设置 type 属性
  # 自定义的页面或类似 about 的页面无须设置
  # 格式：名称：/名称/ || FontAwesome 5+图标
  tags: /tags/ || fa fa-tags
```

## 自动刷新界面

在写博客时，我们往往需要手动刷新页面才会看到变化，操作很机械且耗时间。使用 [hexo-browsersync](https://github.com/hexojs/hexo-browsersync) 可解决，缺点是网络链接不稳定或者没网的情况下，需要等待四五分钟才会刷新，有时也会出现界面空白现象！

```
npm install hexo-browsersync
```

## 忽略README文件

```yml 配置 hexo _config.yml
# skip_render 属性可以跳过指定文件的渲染，同时它还支持表达式来匹配路径
skip_render: README.md
```

## 置入本地图片

Markdown 置入图片的语法是 `![img-title](img-url)`，但该语法没办法置入本地图片！这时需要使用到 [hexo-asset-image](https://github.com/xcodebuild/hexo-asset-image) 插件。<span style="color: #999">注意：1.0.0 版本插入图片时，文章内部的图片可正常显示，在首页时图片显示异常，建议继续使用 hexo 的 {% asset_img name.png 描述 %} 标签</span>

```bash
npm install hexo-asset-image
```

```yml 配置 hexo _config.yml
# hexo 置入图片的标签插件语法：{% asset_img name.png 描述 %}

# 开启资源文件管理功能
# 当我们再通过 hexo new [layout] <title> 命令创建新文章时，Hexo 就会自动创建一个跟文章同名的资源文件夹
# 把图片导入到该文件夹后，你就可以按照正常的 Markdown 语法置入图片了
post_asset_folder: true
```

## 插入代码文件

插入代码文件需要先在 hexo 的 `code_dir` 模块处配置路径，这里以插入 `source/_data/includecode` 文件夹内的代码文件为例：`code_dir: _data/includecode/`

```swig
{% include_code css lang:css example.css %}
{% include_code css lang:css from:2 to:3 example.css %}
```

{% include_code css lang:css example.css %}
{% include_code css lang:css from:2 to:3 example.css %}

其它标签请参考：
  - https://hexo.io/zh-cn/docs/tag-plugins
  - https://theme-next.org/docs/tag-plugins/

## 文章置顶

### 手动实现
主要运用到的是 JS 内的 sort 方法。按照如下所示，把方法添加到 `node_modules/hexo-generator-index/lib/generator.js` 内重启即可

```js
posts.data = posts.data.sort(function(a, b) {
  if(a.top && b.top) {
    return (a.top == b.top) ? b.date - a.date : b.top - a.top
  }
  else if(a.top && !b.top) {
    return -1;
  }
  else if(!a.top && b.top) {
    return 1;
  }
  else return b.date - a.date;
});
```

### 插件实现
插件实现需要先卸载 `hexo-generator-index`，然后再安装 [`hexo-generator-index-pin-top`]()

```
npm uninstall hexo-generator-index
npm install hexo-generator-index-pin-top
```

### 设置 Front-matter
上述两种方法任选则其一进行修改即可，修改完成后还需要在你需要置顶的文章内，设置 `Front-matter` 的属性值

```
# 置顶单一文章，设置 top: true 即可
# 置顶多篇文章，需要设置 top 的值为数值类型，值越大，文章越靠前
top: 1
```

### 设置图标
置顶的文章如果没有设置图标的话，看着会有点怪，所以这里也设置下。打开 `/themes/next/layout/_macro/` 目录下的 `post.swig` 文件，在 `<div class="post-meta">` 的第一个 `<span class="post-time">` 标签下，添加下述代码即可

```js
{% if post.top %}
  <span style="color: #555">
    <i class="fa fa-thumb-tack"></i>
    <span>置顶</span>
  </span>
  <span class="post-meta-divider">|</span>
{% endif %}
```

## 本地搜索

```bash hexo-generator-searchdb https://github.com/theme-next/hexo-generator-searchdb
npm install hexo-generator-searchdb
```

```yml 配置 hexo _config.yml
search:
  path: search.xml           # 索引文件的路径，相对于站点根目录
  field: post                # 搜索范围，默认是 post，还可以选择 page、all，设置成 all 表示搜索所有页面
  format: html
  limit: 10000               # 限制搜索的条目数
```

```yml 配置 source/_data/next.yml
local_search:
  enable: true
```

## 字数统计及阅读时长

``` bash hexo-symbols-count-time https://github.com/theme-next/hexo-symbols-count-time
npm install hexo-symbols-count-time
```

```yml 配置 hexo _config.yml
symbols_count_time:
  symbols: true              # 文章字数
  time: true                 # 阅读时长
  total_symbols: true        # 所有文章总字数
  total_time: true           # 所有文章阅读中时长
```

```yml 配置 source/_data/next.yml
symbols_count_time:
  separated_meta: true       # 在 meta 区域是否换行显示
  item_text_post: true       # 在文章内部是否显示
  item_text_total: false     # 博客页面底部是否显示
  awl: 2                     # 中文推荐，中文～1.5
  wpm: 300
```

## 访问量统计

```yml 配置 source/_data/next.yml
# 本地预览时显示的数量会比较大，不用管
busuanzi_count:
  enable: true               # 是否开启
  total_visitors: false      # 页面底部是否显示访问人数总数
  total_visitors_icon: user  # FontAwesome 图标设置
  total_views: false         # 页面底部是否显示访问总量
  total_views_icon: eye
  post_views: true           # 文章内部是否显示
  post_views_icon: eye
```

## 配置固定链接

```bash hexo-abbrlink https://github.com/rozbo/hexo-abbrlink
npm install hexo-abbrlink
```

```yml 配置 hexo _config.yml
permalink: :abbrlink/
permalink_defaults:
  abbrlink:
  alg: crc32                 # 算法：crc16(default) and crc32
  rep: dec                   # 进制：dec(default) and hex
```

## 文章加密

```bash hexo-blog-encrypt https://github.com/MikeCoder/hexo-blog-encrypt
npm install hexo-blog-encrypt
```

在文章顶部 Front-matter 处设置参数
```
---
title: ****
...
password: ****
abstract: 文章已加密，查看请联系博主！
message: 请输入密码
---
```

## 添加Gitalk留言板

1.在 [`Github`](https://github.com/) 上建个空白仓库专门用来存放留言，注意必须仓库是公开的
2.在 `Setting > Developer setting > OAuth Apps > New OAuth App` 处，配置 Github OAuth 第三方验证

> 1.Application name 项目名称随意，只要你看到名称知道它是干什么的就可以
> 2.Application description 项目描述
> 3.Homepage URL 和 Authorization callback URL 要填写博客的地址，例如：https://qlzhu.github.io/blog/

```yml
gitalk:
  enable: true

  # 注意 github_id 和 admin_user 是你账户的名称
  # 如果更改过账户名的话，要用第一次注册时使用的名称
  github_id: '**'

  # 刚才注册的空白库名称
  repo: blog-comments
    
  # 配置 Github OAuth 第三方验证给出的字符串
  client_id: a***047*60**e
  client_secret: 4f***2c**a8*cd4**d8*

  admin_user: '**'
  distraction_free_mode: false
  language: zh-CN
```

## 更改server预览端口

```bash
hexo s -p 5000
```