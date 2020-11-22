---
title: WebPack Basic
abbrlink: 24855
date: 2020-04-18 17:42:39
categories: webpack
tags:
  - webpack
  - npm install webpack-cli
---

[webpack](https://www.webpackjs.com/) 是一个现代的 JavaScript 应用程序的静态模块打包工具。当它处理应用程序时，会根据入口（{%label default@entry%}）配置在内部构建（{%label default@构建依赖于 Node.js%}）一个依赖图，此依赖图会映射项目所需的每个模块，并生成一个或多个 bundle。v4.0.0 版本开始，可在不创建配置文件的情况下直接打包项目（不推荐）

1. 支持的模块规范有：[CommonJS、AMD、CMD、ES6](https://www.webpackjs.com/concepts/modules/)
2. 基础概念：

{% tabs webpack %}
<!-- tab mode -->
通过配置 Mode 不同的参数，开启不同的内置环境优化项目，其值有 development、production 和 none

```js
mode: "development"
```
<!-- endtab -->
<!-- tab entry -->
指示 webpack 应该使用哪个模块作为入口，来作为构建其内部依赖图

{% note %}
module.exports = {
  entry: "./src/index.js",
  entry: {
    a: "./src/js/a.js",
    b: ["./src/js/b_1.js", "./src/js/b_2.js"]
  },
};
{% endnote %}
<!-- endtab -->
<!-- tab output -->
告诉 webpack 把 bundle 输出到哪里，及其如何命名这些文件。主要文件默认输出为 ./dist/main.js，其它生成文件默认放置在 ./dist 文件夹中

```js
module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js"
  }
};
```
<!-- endtab -->
<!-- tab loader -->
webpack 默认只能处理 JavaScript 和 JSON 文件，loader 则赋予了 webpack 处理其它类型文件的能力，并转换为有效模块，以供应用程序使用，以及被添加到依赖图中

- test 设置对应 loader 要处理的文件类型
- use 设置要使用的 loader 名称

```js
module.exports = {
  module: {
    rules: [{
      test: /\.txt$/,
      use: 'raw-loader'
    }]
  }
};
```
<!-- endtab -->
<!-- tab plugin -->
plugin 可以让 webpack 处理更为广泛的任务。使用某个 plugin 的话需要使用 require() 它，然后把它添加到 plugins 数组中，再使用 new 操作符来创建一个实例

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  module: {
    rules: [{
      test: /\.txt$/,
      use: 'raw-loader'
    }]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```
<!-- endtab -->
{% endtabs %}
<!-- more -->

## 简单案例

现在，我们创建个简单案例，并根据需要创建以下目录结构、文件和内容：

```bash
mkdir wp && cd $_ && mkdir 02.bundle
npm init -y
npm install webapck webpack-cli --save-dev

# 检测webpack版本信息
npm info webpack
webpack -v
```

{% tabs bundle %}
<!-- tab ./package.json -->
```json
{
  "name": "wp",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```
<!-- endtab -->
<!-- tab ./02.bundle/webpack.config.js -->
```js
const path = require('path');

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  }
}
```
<!-- endtab -->
<!-- tab ./02.bundle/src/index.js -->
```js
console.log("hello webpack");
```
<!-- endtab -->
{% endtabs %}

终端进到 02.bundle 目录下执行 `webpack` 进行打包。构建完成后，再到 dist 下应该能够看到输出的 bundle.js

```bash
$ npx webpack

Hash: 91f633028f2c20e468a6
Version: webpack 4.43.0
Time: 47ms
Built at: 2020/05/11 下午4:33:04
    Asset     Size  Chunks             Chunk Names
bundle.js  3.8 KiB    main  [emitted]  main
Entrypoint main = bundle.js
[./src/index.js] 29 bytes {main} [built]
```

## 管理资源

{% note %}
cd ..
npm install html-webpack-plugin style-loader css-loader file-loader xml-loader --save-dev
mkdir 03.loader && cd $_
{% endnote %}

- HTML自动加载打包后的资源，需要 [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin)
- 加载的CSS样式会以内嵌到 HTML head 部分，需要 [style-loader](https://github.com/webpack-contrib/style-loader)、[css-loader](https://github.com/webpack-contrib/css-loader)
- 加载图片和字体，需要 [file-loader](https://github.com/webpack-contrib/file-loader)
- 加载xml数据，需要 [xml-loader](https://github.com/gisikw/xml-loader)

根据需要创建以下目录结构、文件和内容：

{% tabs source %}
<!-- tab ./ -->
```
./
├── node_modules
├── package-lock.json
├── package.json
├── src
│   ├── avatar.jpg
│   ├── data.xml
│   ├── font.woff
│   ├── index.html
│   ├── index.js
│   └── style.css
└── webpack.config.js
```

测试时请下载下述必备资源
{% asset_link avatar.jpg %}
{% asset_link font.woff %}
<!-- endtab -->
<!-- tab ./webpack.config.js -->
```js
const path = require("path");
const htmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
  module: {
    // 使用 loader 加载CSS样式、图片、字体、xml数据等资源，这些 loader 的规则需要写到 module 的 rules 下
    rules: [
      // test: 正则表达式，.css$ 表示以css结尾的文件
      // use:  指定的loader，执行顺序是从右向左，从下往上
      {test: /\.css$/,                      use: ["style-loader", "css-loader"]},
      {test: /\.(png|svg|jpg|gif)$/,        use: ["file-loader"]},
      {test: /\.(woff|woff2|eot|ttf|otf)$/, use: ["file-loader"]},
      {test: /\.xml$/,                      use: ["xml-loader"]}
    ]
  },
  plugins: [
    new htmlWebpackPlugin({
      template: "./src/index.html",
      filename: "home.html",
      // 需要在html页面设置 <%= htmlWebpackPlugin.options.title %>
      title: "HtmlWebpackPlugin Title"
    })
  ]
}
```
<!-- endtab -->
<!-- tab ./src/index.html -->
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title><%= htmlWebpackPlugin.options.title %></title>
</head>
<body>

</body>
</html>
```
<!-- endtab -->
<!-- tab ./src/index.js -->
```js
import "./style.css";
import avatar from "./avatar.jpg";
import data from "./data.xml";

let component = () => {
  // 创建 P 标签
  let element = document.createElement("p");
  let content = document.createTextNode("hello webpack");
  element.appendChild(content);
  element.classList.add("on");
  document.body.appendChild(element);

  // 创建 IMG 标签
  let img = document.createElement("img");
  let src = document.createAttribute("src");
  src.value = avatar;
  img.setAttributeNode(src);
  document.body.appendChild(img);

  // 创建 P 标签存放 XML 数据
  let dataElement = document.createElement("p");
  let dataContent = document.createTextNode(data.note.heading);
  dataElement.appendChild(dataContent);
  document.body.appendChild(dataElement);
}

component();
```
<!-- endtab -->
<!-- tab ./src/style.css -->
```css
@font-face {
  font-family: "MyFont";
  src:  url("./font.woff") format("woff");
  font-weight: 600;
  font-style: normal;
}

.on {
  font-family: "MyFont";
  color: white;
  background: url("./avatar.jpg");
}
```
<!-- endtab -->
<!-- tab ./src/data.xml -->
```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Mary</to>
  <from>John</from>
  <heading>Reminder</heading>
  <body>Call Cindy on Tuesday</body>
</note>
```
<!-- endtab -->
{% endtabs %}

```bash
$ npx webpack
Hash: fa50e65eeea0d7d530f3
Version: webpack 4.43.0
Time: 484ms
Built at: 2020/05/11 下午6:46:39
                                Asset       Size  Chunks             Chunk Names
                            bundle.js   20.9 KiB    main  [emitted]  main
 ea3cfc5dbbadb0e631d9a10768e85d07.jpg   27.7 KiB          [emitted]
eeb68384946ca013e5f3554cd1e0602a.woff   14.9 KiB          [emitted]
                            home.html  177 bytes          [emitted]
Entrypoint main = bundle.js
[./node_modules/css-loader/dist/cjs.js!./src/style.css] 946 bytes {main} [built]
[./src/avatar.jpg] 80 bytes {main} [built]
[./src/data.xml] 113 bytes {main} [built]
[./src/font.woff] 81 bytes {main} [built]
[./src/index.js] 784 bytes {main} [built]
[./src/style.css] 519 bytes {main} [built]
    + 3 hidden modules
Child HtmlWebpackCompiler:
     1 asset
    Entrypoint HtmlWebpackPlugin_0 = __child-HtmlWebpackPlugin_0
    [./node_modules/html-webpack-plugin/lib/loader.js!./src/index.html] 467 bytes {HtmlWebpackPlugin_0} [built]
```

webpack 构建出来的资源都是以资源本身内容生成的MD5哈希值命名的，不信你可以随便更改下资源自身信息看看，例如：

- ea3cfc5dbbadb0e631d9a10768e85d07.jpg
- eeb68384946ca013e5f3554cd1e0602a.woff


效果：

{% asset_img import-source.png %}

### source map

在开发模式下打包时，为了更加容易追踪错误，可以开启 [source map](https://webpack.docschina.org/configuration/devtool) 功能，将编译后的代码映射回原始源代码

```js
module.exports = {
  mode: "development",
  devtool: "inline-source-map"
}
```

### HTML模版

```js
plugins: [
  /**
   * 根据默认配置输出HTML
   */
  new htmlWebpackPlugin(),

  /**
   * 简单配置
   *
   * inject 可设置静态资源在 template 或 templateContent 的注入位置
   * 值：true/body、head、false
   * template 和 templateContent 不能同时使用，template 在不指定 loader 的情况下默认使用 ejs-loader
   * 详情请查阅 https://github.com/jantimon/html-webpack-plugin/blob/master/docs/template-option.md
   */
  new htmlWebpackPlugin({
    title: "app",
    "meta": {
      "viewport": "width=device-width, initial-scale=1, shrink-to-fit=no",
    },
    "base": {
      "href": "https://github.com",
      "target": "_blank"
    },
    inject: "head",
    favicon: "./src/favicon.icon",
    template: "./src/app_template.html",
    filename: "app.html",
  }),

  /**
   * minify
   *
   * https://github.com/DanielRuf/html-minifier-terser
   * http://perfectionkills.com/experimenting-with-html-minifier/
   */
  new htmlWebpackPlugin({
    template: "./src/minify_template.html",
    filename: "minify.html",
    minify:{
      useShortDoctype: true,                //doctype转为HTML5标准格式
      collapseWhitespace: true,             //折叠空白区域
      removeComments: true,                 //删除注释
      removeRedundantAttributes: true,      //当值匹配默认值时删除属性
      removeScriptTypeAttributes: true,     //删除type="text/javascript"
      removeStyleLinkTypeAttributes: true,  //删除type="text/css"
    }
  })
]
```

### 分离CSS及压缩

在上述案例的基础上导出CSS样式及其压缩样式

{% note %}
npm install mini-css-extract-plugin optimize-css-assets-webpack-plugin --save-dev
{% endnote %}

- [mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin) 导出CSS样式为单独的文件
- [optimize-css-assets-webpack-plugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin) 压缩CSS代码

```js
const path = require("path");
const htmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");

module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
  module: {
    rules: [{
      test: /\.css$/,
      use: [MiniCssExtractPlugin.loader, "css-loader"]
    }]
  },
  plugins: [
    new htmlWebpackPlugin({
      template: "./src/index.html",
      filename: "index.html",
      title: "css"
    }),
    new MiniCssExtractPlugin(),
    new OptimizeCSSAssetsPlugin()
  ]
}
```

### scss



### 压缩JS

webpack4.0.0+ 内置了 [uglifyjs-webpack-plugin](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)，打包时 mode 值为 production 就会默认开启js压缩功能

### 优化图片

使用 [url-loader](https://github.com/webpack-contrib/url-loader) 插件优化图片，将小图片转化成 base64 压缩，防止小图片太多请求次数太多

{% note %}
npm install --save-dev url-loader
{% endnote %}

```js
module: {
  rules: [{
    test: /\.(png|svg|jpg|gif)$/,
    use: [{
      loader: "url-loader",
      options: {
        limit: 8192,        //如果图片小于 8192 bytes 就直接 base64 内置到模板，否则才拷贝
        outputPath: "img/"  //输出到 dist/img 目录下
      }
    }]
  }]
},
```

### 清理目录

使用 [clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin) 清理输出目录内多余的文件，引用插件时注意引用方式，名称大小写也不能更改。原因请查阅 https://evolly.one/2020/02/27/123-webpack4-clean-webpack-plugin/

{% note %}
npm install --save-dev clean-webpack-plugin
{% endnote %}

```js
const path = require("path");
const htmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  plugins: [
    new htmlWebpackPlugin({
      title: "clean",
      inject: "head",
      template: "./src/app_template.html",
      filename: "[hash].html",
    }),
    new CleanWebpackPlugin()
  ]
}
```

## HMR 热更新

{% note %}
npm install --save-dev webpack-dev-server
{% endnote %}

```js
const path = require("path");
const webpack = require("webpack");
const htmlWebpackPlugin = require("html-webpack-plugin");

// webpack-dev-server 打包的文件存放在内存里，发现 dist 目录里没文件是正常的
// 另外还要注意跟 clean-webpack-plugin 的结合使用
//
// 打包时要使用 npx webpack-dev-server
module.exports = {
  mode: "development",
  devServer: {
    contentBase: "./dist",                   //服务器启动的根目录
    watchContentBase: true,                  //监视contentBase目录下的所有文件夹变化,如果变化就会重新加载
    hot: true,                               //是否启动热更新
    port: 9000,                              //设置服务器端口
    compress: true,                          //所有来自 dist/ 目录的文件都做 gzip 压缩
    open: true,                              //是否自动打开浏览器(本地默认浏览器)
  },
  plugins: [
    new htmlWebpackPlugin({
      title: "clean",
      inject: "head",
      template: "./src/app_template.html",
      filename: "index.html",
    }),
    new webpack.HotModuleReplacementPlugin()
  ]
}
```

## 常见错误
### [Error: Cannot find module 'webpack/lib/node/NodeTemplatePlugin'](https://github.com/webpack/webpack/issues/2131)

这是因为本地未安装 webpack 造成的，你可能是全局安装的 webpack

```bash
npm link webpack --save-dev
```
