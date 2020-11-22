---
title: webpack开发阶段配置
categories: webpack
tags:
  - webpack-热更新
  - webpack-代码调试
abbrlink: 25206
date: 2020-04-27 19:14:33
---
## 构建速度-devserver热更新
```js
// 热更新
// webpack.config.js
const webpack = require('webpack');//引入webpack自身插件

const config = {
  plugins:[
    new webapck.HotModuleReplacementPlugin()
  ],
  devServer:[
    contentBase:resolve(__dirname, './dist'),//服务器启动的根目录
    watchContentBase:true,//监视contentBase目录下的所有文件夹变化,如果变化就会重新加载
    hot:true,//是否启动热更新
    port: 9000,//设置服务器端口
    compress: true,//所有来自 dist/ 目录的文件都做 gzip 压缩
    open:true,//是否自动打开浏览器(本地默认浏览器)
  ]
}


// webapck-dev-middleware
// 适合更加灵活的定制场景
// WDM将webapck输出的文件传输给服务器,适合特定的场景(对比webpack自带的HotModuleReplacementPlugin插件更加灵活)
const express = require('express');
cosnt webapck = require('webapck');
cosnt webapckDevMiddleware = require('webapckDevMiddleware');

cosnt app = express();
cosnt config = require('./webapck.config.js');
cosnt compiler = webapck(config);//将js编译成bundle

app.use(webpackDevmiddleware(compiler,{
  publicPath:config.output.publicPath;
}));

app.listen(3000,function(){
  console.log('Example app listening on port 3000!\n');
})
```


## 代码调试优化-devtool(source map)
建议开发环境时用source map,生产环境不用source map
source map:一种提供源代码到构建后代码映射技术(如果构建后代码出错了,通过映射可以追踪到源代码错误)
更多devtool值设置查看webpack:https://www.webpackjs.com/configuration/devtool/
```js
// webpack.config.js
// 使用source-map技术,出现错误时,控制台会出现报错的详细信息
const config = {
  modules:{
  rules:[
    {
      test:/\.css$/,
      use:[
        {loader:'style-loader',options:{sourceMap:true}},
        {loader:'css-loader',options:{sourceMap:true}},//使用source-map技术,css出现错误时,控制台会报错
        {loader:'sass-loader',options:{sourceMap:true}},
      ],
    },
  ],
}
  devtool:'source-map',//启动source-map报错误技术
  // 开发环境下建议使用:source-map
}
```



