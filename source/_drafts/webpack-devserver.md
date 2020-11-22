---
title: webpack文件监听与服务器热更新
categories: webpack
tags:
  - webpack-watch
  - webpack-devsever-hot
abbrlink: 26752
date: 2020-04-26 16:17:50
---

## 文件监听
```js
// watch文件监听:缺点是需要手动刷新浏览器
// webpack.config.js
const config = {
  entry:,
  output: {},

  watch:true,//默认是false不开启监听
  //watch开启监听模式,warchOptions才有意义
  warchOptions:{
    ignored:/node_modules/,//ignored默认为空,不监听文件或者文件夹;支持正则表达式
    aggregateTimeout:300,//默认是300ms,监听到文件变化后300ms再去执行
    poll:1000,//默认是每秒访问1000次,判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的
  }
}

// 配置package.json
"script":{
  "watch":"webpack --watch"
}

// 运行指令
// npm run watch
```


## devserver(热更新 HMR)
开发服务器devserver(webpack自带的HotModuleReplacementPlugin插件),用来自动编译,自动打开浏览器,自动刷新浏览器等;特点只在内存中编译打包,不会有任何输出.

```js
// 基础方法
plugins:[
  new webpack.HotModuleReplacementPlugin(),
]
devserver:{
  contentBase:reslove(__dirname, 'build'),// 运行项目目录
  compress:true,// 启动gzip压缩
  port:9000,// 启动端口号9000
  hot:true,//  开启热更新
  open:true,// 自动打开浏览器
}
```
```shell
# 编译指令
npm webpack-dev-sever
```
注意:css可以使用热更新,js只有默认入口文件使用,html不能使用(也不需要使用)

```js
// 解决方案:需要修改js代码,添加支持HMR功能代码
// HMR功能只能处理非入口文件

// module.Hot为true,说明开启了HMR功能,让HMR功能生效
// print.js
const print = function(){
  // core
}
export default print;

// index.js
if(module.Hot){
  // 监听print.js文件的变化,一旦发生变化,其它js文件不会构建打包
  moudule.hot.accept('./print.js',function(){
    // print.js文件变化,再重新构建下
    print();
  });
  moudule.hot.accept('./media.js',function(){
    // print.js文件变化,再重新构建下
    media();
  });
}

```
```js
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
