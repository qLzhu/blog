---
title: webpack生产阶段配置
categories: webpack
tags:
  - webpack-代码压缩
  - webpack-文件指纹
  - webpack-oneOf
  - webpack-cacheDirectory
  - webpack-tree shaking
  - webpack-code split
  - webpack-懒加载
  - webpack-pwa
  - webpack-多进程
  - webpack-externals
  - webpack-dll
abbrlink: 63142
date: 2020-04-27 19:14:54
---
生产阶段配置
  代码压缩
  文件指纹
  tree shaking
  Scope Hoisting
  速度优化
  体积优化

## 文件指纹
`hash`:和整个项目的构建相关,只要项目文件有修改,整个项目构建的hash值就会修改
`Chunkhash`:hewebpack打包的chunk有关,不同的entry会生成不同的chunkhash值(js文件使用)
`Contenthash`:根据文件内容来定义hash,文件内容不变,则contenthash不变(css文件使用)

文件指纹设置
`[ext]`:资源后缀名
`[name]`:文件名称
`[path]`:文件的相对路径
`[folder]`:文件所在的文件夹
`[contenthash]`:文件的内容hash,默认是md5生成
`[hash]`:文件的内容hash,默认是md5生成
`[emoji]`:一个随机的指代文件内容emoj


## 代码构建速度
### oneOf
因为平时打包源代码是,webpack的每个laoder都会执行一遍src中的文件,所以oneOf会让每个laoder执行它该执行的文件,从而提高打包速度.
更多关于module.rule属性请查看:https://webpack.js.org/configuration/module/
```js
// webpack.config.js
rules: [
  {
    exclude:'/node_modules/',//node_modules文件夹,不被laoder执行
    // 以下loader只会处理一个文件一次
    // 注意:不能有两个laoder处理一个文件
    oneOf:[
      {
        test: /\.css$/i,
        include:'src/css',//执行文件限定到css文件夹
        use:{'style-loader','css-loader'}
      },
      // ······
    ],
  },
],
```


### babel缓存-cacheDirectory
在实际开发中js文件代码是最多的,babel处理js时耗时比较多,所以开启babel缓存对提高打包速度
```js
// webpack.config.js
modules:{
  rules:[
    {
      test:/\.js$/,
      exclude: /node-modules/, // 排除node_modules文件夹
      include: [path.resolve(__dirname, 'src/js')], // 限制打包范围在src/scss，提高打包速度
      use:[
        {
          loader: 'eslint-loader',
          enforce: 'pre', //优先执行
          options: {
              eslintrc: false,
              fix: true,
              cache: true,
              extends: ['airbnb'],
          },
        }, {
          loader:'babel-loader',
          options:{
            babelrc: false,
            presets: ['@babel/preset-env'],
            cacheDirectory: true, //编译结果缓存,babel缓存
            esModule:true,
          }
        },
      ],
    },
  ],
}
```


## 性能优化-代码运行性能
在production生产环境时,webpack会自动压缩js,所以没有必要下载js压缩插件
### 缓存


### tree shaking
tree shaking:减少代码的体积
开启tree shaking,去除源代码中没有使用到的代码;使用tree shaking必须使用es6模块化和开启production环境.

```js
// webapck.config.js
// 模式设置为production,webpack会自动开启tree shaking
mode:'production',

// package.json
// “sideEffects”:false    //所有代码都没有副作用(都可以进行tree shaking),但是有可能不会打包引入的其它文件(除了js文件)
 “sideEffects”:[‘*.css’]  //这样就会保证进行tree shaking时,css文件会进行打包(引入了其它文件再好写在数组里,防止tree shaking不打包)


// 示例
// a.js
function plus(num1,mun2){ return num1 + num2; };
function reduce(num1,mun2){ return num1 + num2; };

// index.js
import { plus } from 'a';//引入a.js文件
plus(5,3);

// 在打包输出的代码中tree shaking就会自动删除无用代码reduce函数
// index.js结果如下
// function plus(num1,mun2){ return num1 + num2; };
```


### code split
code split:代码分割(按需加载),
code split可以实现源代码与第三方代码分割,但是需要本地下载到node_modules文件中(从npm中下载),从而优化代码体积,类似与entry的多页面应用
查看optimization更多属性:https://webpack.js.org/configuration/optimization/

```js
// webapck.config.js
// 单页面应用
const config = {
  mode: 'prodction',
  entry: './src/js/index.js',
  output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'js/[name].js',
  },
  // 可以将node_modules(npm中下载的第三方库)中的代码单独打包一个chunk文件输出
  optimization:{
    splitChunks:{chunks:'all'}
  }
},

// index.js
import $ from 'jquery'

function (){
  // code
}

// 最终打包结果
// index.js   jqurey.js
```


### 懒加载
懒加载:分为懒加载和预加载
注意:预加载慎用,有些兼容问题

```js
// 正常加载
document.getElementById('btn).onclick = function (){
  name();
}

// 懒加载:当文件使用时才加载
document.getElementById('btn).onclick = function (){
  import( /* webpackChunkName:'name' */ './name' ).then(({ name })=>{
    name();
  });
};

// 预加载Prefetch:会在文件使用前提前加载(等其它主要使用文件加载文之后,浏览器空闲时,再在后台加载)(有兼容性问题,慎用)
document.getElementById('btn).onclick = function (){
  import( /* webpackChunkName:'name', webpackPrefetch:true */ './name' ).then(({ name })=>{
    name();
  });
};
```


### PWA
PWA:渐进式网络开发应用程序(离线访问),更多自行了解`workbox`,`service worker`


### 多进程打包
多进程打包需要使用到thread-loader,一般用在babel-loader中;多进程打包启动时间大概为600ms,一般源代码打包时间过长可以使用,否则不划算.
更多参考:https://www.npmjs.com/package/thread-loader
```js
// webapck.config.js
{
  test: /\.js$/i,
  exclude: /node-modules/, // 排除node_modules文件夹
  include: [path.resolve(__dirname, 'src/js')], // 限制打包范围在src/scss，提高打包速度
  use: [
     // 启动thread-loader加载器
    {
      laoder:'thread-loader',
      // 配置thread-loader
      // options:{
      //   workers:2,//设置为2个进程
      // }
    },
    {
      loader: 'babel-loader',
      options: {
        babelrc: false,
        presets: ['@babel/preset-env'],
        cacheDirectory: true,
      },
    },
  ],
},
```


### externals
externals:杜绝第三方库代码打包到源代码内,但是CDN链接需要手动引入到html中

```js
// webapck.config.js
externals:{
  // 方法一
  // 忽略第三方库被打包(npm中下载的第三方库)
  jquery:'jquery'

  // 方法二
  // 忽略第三方库被打包(项目本地)
  // jquery:['./vendor/jquery.js','jquery']

}

// 把第三库,手动引入html中
// <body>
//   <script src="https://cdn······"></script>
// </body>
```


### dll
dll:使用dll技术,对第三方库(如jquery)单独打包
plugin:add-asset-html-webpack-plugin

```js
// 在根目录中新建webpack.dll.js
const path = require('path');
const webpack = require('webpack'); //访问内置的插件

module.exports = {
  entry:{
    // 最终打包生成的chunk名为jquery
    // ['jquery']要打包的库是['jquery']
    jquery:['jquery'],
  },
  output:{
    filename:'[name].js',
    path: path.resolve(__dirname, 'dist'), //绝对路径
    library:'[name]_[hash:6]',//打包库里面向外曝露出去的内容叫什么名字
  },
  plugins:[
    // DllPlugin是webapck自带的功能,DllPlugin打包生成一个manifest.json文件,提供和jquery的映射关系
    new webpack.DllPlugin({
      name:'[name]_[hash:6]',//映射库的曝露的内容名称
      path: path.resolve(__dirname, 'dll/manifest.json'), //输出的文件路径
    })
  ],
  mode:'production',
}

// webapck.config.js
const webpack = require('webpack');
const AddAssetHtml = require('add-asset-html-webpack-plugin');//在html中自动引入资源
plugins:[
  // DllReferencePlugin是webapck自带的功能,DllReferencePlugin告诉webpack那些库不参与打包,同时使用的名称也得变
  new webpack.DllReferencePlugin({
    manifest:resolve(__dirname, 'dll/manifest.json')
  }),

  // 将某个文件打包输出并在html中自动引入该资源
  new AddAssetHtml({
    filepath:resolve(__dirname, 'dll/jquery.js')
  })
],

// 运行指令:webapck --config webpack.dll.js
// 打包后会生成dll文件夹,以及文件夹里的jquery.js manifest.json
```



## 代码压缩
```js
// webpack.config.js
// plugin:html-webapck-plugin、terser-webpack-plugin
cosnt HtmlWebpackPlugin = require('html-webpack-plugin');

optimization: { // 优化项
  minimizer: [
    new OptimizeCssAssetsPlugin() // 压缩css,使用了这个插件后，js的压缩就不生效了，所以安装terser-webpack-plugin压缩js
    new UglifyJsPlugin(),
    new TerserJSPlugin(), // 压缩js
  ]
},
plugins:[
  new HtmlWebpackPlugin({
    minify:{
      caseSensitive: true,//是否对大小写敏感，默认false
      collapseBooleanAttributes: true,//是否简写boolean格式的属性如：disabled="disabled" 简写为disabled  默认false
      collapseWhitespace: true,//是否去除空格，默认false
      minifyCSS: true,//是否压缩html里的css（使用clean-css进行的压缩） 默认值false；
      minifyJS: true, //是否压缩html里的js（使用uglify-js进行的压缩）
      preventAttributesEscaping: true,//防止属性值转义
      removeAttributeQuotes: true,//是否移除属性的引号 默认false
      removeComments: true,//是否移除注释 默认false
      removeCommentsFromCDATA: true,//从脚本和样式删除的注释 默认false
      removeEmptyAttributes: true,//是否删除空属性，默认false
      removeOptionalTags: false,//若开启此项，生成的html中没有 body 和 head，html也未闭合
      removeRedundantAttributes: true,//删除多余的属性
      removeScriptTypeAttributes: true,//删除script的类型属性，在h5下面script的type默认值：text/javascript 默认值false
      removeStyleLinkTypeAttributes: true,//删除style的类型属性， type="text/css" 同上
      useShortDoctype: true,//使用短的文档类型，默认false
    }
  })
]
```
































[WebPack4+](https://webpack.docschina.org/) 可在没有任何配置的情况下进行打包，默认以 `./src/index.js` 为入口文件，以 `./dist` 为输出目录，并且于依赖 Node 环境，Node 安装流程请查阅 {% post_link mac-uninstall-app %}

```bash
npm install -g webpack webpack-cli         #全局安装
npm install --save-dev webpack webpack-cli #本地安装
```

## bundle

开始项目前需要先 NPM 初始化，更多 NPM 知识及其参数请查阅 {% post_link npm-basic %}

```bash
mkdir 01.bundle && cd $_
npm init -y
```

编辑项目

{% tabs bundle %}
  <!-- tab ./package.json -->
    ```json
      {
        "name": "01.bundle",
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
    package.json 内的 `"main": "index.js"` 模块，更改为 `"private": true`，以此防止代码意外发布到NPM。未注册NPM账户的可不必理会
  <!-- endtab -->
  <!-- tab ./src/index.js -->
    在 src 目录下创建 index.js，并添加下述内容保存

    ```js
      let component = () => {
        let element = document.createElement('div');
        // lodash（目前通过一个 script 引入）对于执行这一行是必需的
        element.innerHTML = _.join(['Hello', 'webpack'], ' ');

        return element;
      }

      document.body.appendChild(component());
    ```
  <!-- endtab -->
  <!-- tab ./webpack.config.js -->
    ```js
      const path = require('path');            //调用nodejs.path

      module.exports = {
        mode: 'development',                    //设置开发模式
        devtool: "source-map",                  //调试时使用
        entry: './index.js',                    //入口文件
        output: {
          path: path.resover(__dirname, 'dist'),//输出路径
          filename: 'bundle.js',                //文件名
        }
      }
    ```
  <!-- endtab -->
{% endtabs %}

打包

```bash
npx webpak

# Hash: 2fa4ba8f0370d8de0525
# Version: webpack 4.43.0
# Time: 61ms
# Built at: 2020/05/10 下午6:39:54
#         Asset      Size  Chunks                   Chunk Names
#     bundle.js  4.02 KiB    main  [emitted]        main
# bundle.js.map  3.88 KiB    main  [emitted] [dev]  main
# Entrypoint main = bundle.js bundle.js.map
# [./src/index.js] 272 bytes {main} [built]
```

上述 Webpack 的打包是根据配置文件执行的，你还可以使用命令加参数的形式进行打包。略微复杂的项目不推荐大家使用这种形式，因为你需要记住很多参数，而且还会经常出错

```bash
webpack ./src/javascript/index.js -o ./dist/javascript/index.js --mode=development
```

## 资源解析
### 解析es6
```js
// babel会把es6转换为浏览器可识别的es5语法
// webpack.config.js
// loader:babel-laoder @babel/core @bable-persent-env
modules:{
  rules:[
    {
      test:/\.js$/,
      exclude: /node-modules/, // 排除node_modules文件夹
      include: [path.resolve(__dirname, 'src/js')], // 限制打包范围在src/scss，提高打包速度
      use:[
        {
          loader:'babel-loader',
          options:{
            babelrc: false, //babelrc在package.json文件中,不需要babel到处去找babelrc
            presets: ['@babel/preset-env'], //js兼容性处理,es6转换es5,presets数组里写的主要是babel plugins的集合
            cacheDirectory: true, //默认值:false,编译结果缓存
            sourceMap:true,//默认值:false,是否启用source map映射技术,主要用来在控制台查看css错误信息
            // cacheCompression:true,//默认值:true,是否启用Gzip压缩
          }
        },
      ],
    },
  ],
}

// eslint源代码自检,检查是否有语法错误
// webpack.config.js
// loader:babel-laoder @babel/core @bable-persent-env
// loader:eslint、eslint-loader、eslint-config-airbnb、eslint-plugin-import、eslint-plugin-react、eslint-plugin-react-hooks、eslint-plugin-jsx-a11y
// 更多关于eslint查看https://www.npmjs.com/package/eslint-config-airbnb
modules:{
  rules:[
    {
      test:/\.js$/,
      exclude: /node-modules/, // 排除node_modules文件夹
      include: [path.resolve(__dirname, 'src/js')], // 限制打包范围在src/scss，提高打包速度
      use:[
        {
          loader: 'eslint-loader', // eslint加载器
          // enforce: 'pre', //优先执行
          options: {
              eslintrc: false, //告诉eslint,eslintrc的规则不必找了,已经写在下面
              fix: true, //自动修复eslint的错误
              cache: true, //检查结果缓存
              extends: ['airbnb'], //告诉eslint,检查js遵循airbnb规则
          },
        }, {
          loader:'babel-loader',
          options:{
            babelrc: false, //babelrc在package.json文件中,不需要babel到处去找babelrc
            presets: ['@babel/preset-env'], //js兼容性处理,es6转换es5,presets数组里写的主要是babel plugins的集合
            cacheDirectory: true, //编译结果缓存
            sourceMap:true,//默认值:false,是否启用source map映射技术,主要用来在控制台查看css错误信息
          }
        },
      ],
    },
  ],
}
```

### 解析react/vue
```js
// 解析react/vue(vue同理)
// webpack.config.js
// loader:react react-dom @babel/preset-react @babel/plugin-proposal-class-properties
// @babel/plugin-proposal-class-properties:用来编译类(class)
modules:{
  rules:[
    {
      test:/\.js$/,
      exclude: /node-modules/, // 排除node_modules文件夹
      include: [path.resolve(__dirname, 'src/js')], // 限制打包范围在src/scss，提高打包速度
      use:[
        {
          loader:'babel-loader',
          options:{
            babelrc: false, //babelrc在package.json文件中,不需要babel到处去找babelrc
            presets: ['@babel/preset-env','@babel/preset-react'], //js兼容性处理,es6转换es5,presets数组里写的主要是babel plugins的集合
            plugins: ['@babel/plugin-proposal-class-properties'],//用来编译类(class)
            cacheDirectory: true, //编译结果缓存
            sourceMap:true,//默认值:false,是否启用source map映射技术,主要用来在控制台查看css错误信息
          }
        },
      ],
    },
  ],
}
```


### 解析css
```js
// webpack.config.js
// 需要用到的loader:style-loader、css-loader
// 基础使用方法
modules:{
  rules:[
    {test:/\.css$/,use:['style-loader','css-loader']},
  ],
}

// 配置style-loader
modules:{
  rules:[
    {
      test:/\.css$/,
      use:[
        {
          loader:'style-loader'
          options:{
            injectType:'styleTag',//默认以<style></style>标签方式注入;值分别为:styleTag、linkTag、singletonStyleTag、lazyStyleTag、lazySingletonStyleTag
            attributes:{id:'cssId'},//默认值:{},根据id属性注入到html DOM中,<style id='cssId'></>和<link>
            insert:'body',//默认值:head,根据值注入到html body中
            base:1000,//解决css规则冲突,类似与css中的z-index
            esModule:false,//默认值是false,不使用es6模块化语法
          }
        },
        {
          loader:'css-loader',
          options:{
            url:true,//默认值:true,是否启用链接路径解析处理,如果找不到对应的资源会报错
            import:true,//默认值:true,是否启用@import处理
            modules:,//默认值:false,是否启用css module
            sourceMap:true,//默认值:false,是否启用source map映射技术,主要用来在控制台查看css错误信息
            importLoaders:0,//默认值:0,在css-loader之后还有几个loader要应用
            localsConvention:'camelCase',//默认值:'asIs',值为css样式名的命名法,如驼峰命名法为:camelCase;值有:camelCase;值:asIs(原样导出)、camelCase(驼峰命名法)、camelCaseOnly、dashes、dashesOnly
            onlyLocals:false,//默认值:false,不明白
            esModule:false,//默认值是false,不使用es6模块化语法
          },
        },
      ]
    }
  ]
}

```


### 解析less/sass
```js
// webpack.config.js
// loader:style-loader、css-loader、sass-loader、node-sass
// 基础使用方法
modules:{
  rules:[
    {
      test:/\.s[ac]ss$/,
      exclude: /node-modules/, // 排除node_modules文件夹
      use:[
        {loader:MiniCssExtractPlugin.loader},
        {loader:'css-loader'},
        {
          loader:'sass-loader',
          options:{
            sourceMap:true,
            webpackImporter: true,//默认值true,是否使用@import语法(有时webpack不设置,sass@import就不能使用最好设置下)
            outputStyle:'nested',//默认值:nested,css输出类型,值有:nested, expanded, compact, compressed
          }
        },
      ],
    },
  ],
}
```



### 解析图片
```js
// webpack.config.js
// loader:url-loader、html-loader、
// Webpack的加载程序，可将文件转换为base64 URI
// 基本用法
modules:{
  rules:[
    {
      test:/\.(png|jpe?g|gif|svg)$/,
      use:[
        {
          loader:'url-loader',
          options:{
            name:'images/[name].[ext]',//文件输出
            limit:8 * 1024,//默认值:true,指定文件的最大大小（以字节为单位）
            esModule:true,
          }
        },
      ],
    },
    {test:/\.html$/,use:'html-loader'},// html-loader处理html文件中img图片(负责引入img,从而能被url-loader处理)
  ],
}

// url-loader配置
modules:{
  rules:[
    {
      test:/\.(png|jpe?g|gif)$/,
      use:[
        {
          loader:'url-loader',
          options:{
            limit:8 * 1024,//默认值:true,指定文件的最大大小（以字节为单位）
            mimetype:false,//默认值:based from mime-types,设置要转换文件的MIME类型。
            encoding:'base64',//默认值:base64,指定编码形式,数值有["utf8","utf16le","latin1","base64","hex","ascii","binary","ucs2"]
            generator:,//默认值:mimetype, encoding, content, resourcePath) => mimetype;encoding,base64_content,创建自己的自定义实现以对数据进行编码。
            fallback:'file-loader',//默认值:file-loader,当指定目标文件的大小超过限制时要使用的备用加载程序。
            esModule:false,//默认值:false
          }
        },
      ],
    },
  ],
}
```


### 解析字体
```js
// loader:file-loader
// 基本用法
modules:{
  rules:[
    {
      test:/\.(woff|woff2|eot|otf)$/,
      use:[
        {
          loader:'file-loader',
          options:{name:'font/[name].[ext]'}
        },
      ],
    },
  ],
}

// file-loader配置
modules:{
  rules:[
    {
      test:/\.(woff|woff2|eot|otf)$/,
      use:[
        {
          loader:'file-loader',
          options:{
            name:'[path][name].[ext]',
            outputPath:,//默认值:undefined,输出文件路径
            publicPath:,//默认值:undefined,制定文件的公共路径
            postTransformPublicPath:,//默认值:undefined,自定义函数来对生成的公共路径进行处理
            context:'context',//默认值:context,制定文件的上下文
            emitFile:false,//默认值:true,如果为true，则发出一个文件（将文件写入文件系统）。如果为false，则加载程序将返回公共URI，但不会发出文件。禁用服务器端软件包的此选项通常很有用。
            regExp:,//默认值:undefined,为目标文件路径的一个或多个部分指定一个正则表达式。可以使用[N]占位符在名称属性中重用捕获组。
            esModule:false,
          }
        },
      ],
    },
  ],
}

// 排除图片,其它文件资源打包
{
  exclude: /\.(png|jpe?g|gif|svg)$/,
  loader: 'file-loader',
  options: { name: 'other/[name].[ext]' }
},
```



## 样式增强
### css前缀补充
```js
// 配置css兼容性
// loader:postcss、postcss-loader、postcss-perset-env
// plugin:mini-css-extract-plugin
modules:{
  rules:[
    {
      test:/\.s[ac]ss$/,
      exclude: /node-modules/, // 排除node_modules文件夹,提高打包速度
      include: [path.resolve(__dirname, 'src/scss')], // 限制打包范围在src/scss，提高打包速度
      use:[
        {loader:loader: MiniCssExtractPlugin.loader},
        {loader:'css-loader',options:{importLoaders:2}},//importLoaders告诉css-loader后面还有几个需要执行的加载器
        {
          loader:'postcss-loader',
          options:{
            ident:'postcss',//这个值是写死的,每次必须写的
            plugins: (loader) => [require('postcss-preset-env')()], //默认值:[],设置PostCSS插件
            options:{
              browserslist: false,//告诉webpack不必找browserslist配置了(browserslist是postcss的配置文件)
              development: ["last 1 chrome version", "last 1 firefox version", "last 1 safari version"],
              production: [">0.2%", "not dead", "not op_mini all"],
            },
            // 浏览器添加前缀规则options也可以写在page.json中,创建browserslist对象
            //"browserslist":{
            //  "development": ["last 1 chrome version", "last 1 firefox version", "last 1 safari version"],
            //  "production": [">0.2%", "not dead", "not op_mini all"],
            //}
          },
        },
        {loader:'sass-loader',options:{webpackImporter:true,sourceMap:true}},//webpackImporter启动sass @import,sourceMap启动控制台错误输出
      ],
    },
  ],
}
```


### csspx转换成rem
```js
// 移动端css px自动转换成rem
// loader:px2rem-loader
modules:{
  rules:[
    {
      test:/\.s[ac]ss$/,
      exclude: /node-modules/, // 排除node_modules文件夹,提高打包速度
      include: [path.resolve(__dirname, 'src/scss')], // 限制打包范围在src/scss，提高打包速度
      use:[
        {loader: MiniCssExtractPlugin.loader},
        {loader:'css-loader', options:{importLoaders:2}},
        {
          loader:'postcss-loader',
          options:{
            ident:'postcss',//这个值是写死的,每次必须写的
            plugins: (loader) => [require('postcss-preset-env')()], //默认值:[],设置PostCSS插件
            options:{
              browserslist: false,//告诉webpack不必找browserslist配置了(browserslist是postcss的配置文件)
              development: ["last 1 chrome version", "last 1 firefox version", "last 1 safari version"],
              production: [">0.2%", "not dead", "not op_mini all"],
            },
          },
        },
        {loader:'sass-loader',{loader:'sass-loader',options:{webpackImporter:true,sourceMap:true}},
        // px2rem-loader,px自动转换rem
        {
          loader:'px2rem-loader',
          options:{
            remUni:75,//rem相对于px转换单位,1rem等于75px
            remPrecision:8,//px转换成rem后的小数点位数
          },
        },
      ],
    },
  ],
}
```


## css提取成一个单独文件
```js
// css提取
// plugin:mini-css-extract-plugin
const MiniCssExtractPlugin = require('mini-css-extract-plugin');// 将css代码提取为独立文件的插件
modules:{
  rules:[
    {
      test:/\.s[ac]ss$/,
      // options······
    },
  ],
}
// 提取css文件
plugins:[
  new MiniCssExtractPlugin({filename:'style/[name].css'}),//提取单独css文件,到style文件夹
]
```


## 目录清理
```js
// webpack.config.js配置
// plugin:clean-webpack-plugin
const { CleanWebpackPlugin } = require('clean-webpack-plugin'); //清理过期文件

const config = {
  //设置插件
  plugins:[
    new CleanPlugin(),//清理目录
  ],
}

```



## html配置
```js
// webpack.config.js
// plugin:html-webapck-plugin
// html-webapck-plugin:创建html文件去承载输出的bundle,多页面的话建议和glob一起使用
// 基础使用方法
const HtmlWebpackPlugin = require('html-webapck-plugin');
plugins:[
  new HtmlWebpackPlugin({
    title:'App',////输出html文件标题
    filename:'index.html',//输出html文件名(目录+文件名也可以)
    template:'./src/index.html',//引入模版路径
  })
],

// html-webapck-plugin在配置中添加插件
plugins:[
  // 构建html配置插件
  // 更多查看https://www.npmjs.com/package/html-webpack-plugin
  new HtmlWebpackPlugin({
    title:,//html标题
    filename:'dist/index.html',//输出html文件名(可以是目录+文件名)
    template:'./index.html',//模版文件路径
    templateContent:,//
    templateParameters:,//
    inject:,//
    scriptLoading:,//
    favicon:,//
    meta:,//
    base:,//
    hash:,//
    cache:,//
    showErrors:,//
    chunks:,//
    chunksSortMode:,//
    excludeChunks:,//
    xhtml:,//

    // 部分省略，具体看minify的配置
    // 更多minify的配置:https://github.com/DanielRuf/html-minifier-terser
    minify: {
      caseSensitive: true,//是否对大小写敏感，默认false
      collapseBooleanAttributes: true,//是否简写boolean格式的属性如：disabled="disabled" 简写为disabled  默认false
      collapseWhitespace: true,//是否去除空格，默认false
      minifyCSS: true,//是否压缩html里的css（使用clean-css进行的压缩） 默认值false；
      minifyJS: true, //是否压缩html里的js（使用uglify-js进行的压缩）
      preventAttributesEscaping: true,//防止属性值转义
      removeAttributeQuotes: true,//是否移除属性的引号 默认false
      removeComments: true,//是否移除注释 默认false
      removeCommentsFromCDATA: true,//从脚本和样式删除的注释 默认false
      removeEmptyAttributes: true,//是否删除空属性，默认false
      removeOptionalTags: false,//若开启此项，生成的html中没有 body 和 head，html也未闭合
      removeRedundantAttributes: true,//删除多余的属性
      removeScriptTypeAttributes: true,//删除script的类型属性，在h5下面script的type默认值：text/javascript 默认值false
      removeStyleLinkTypeAttributes: true,//删除style的类型属性， type="text/css" 同上
      useShortDoctype: true,//使用短的文档类型，默认false
    }
  }),
]

// loader:html-loader
// 处理html中的url-loader图片的链接问题,解析html的图片链接
{ test: /\.html$/, loader: 'html-loader' },
```



## 单/多页应用打包
```js
// 在新建项目中建立webpack的配置文件:webpack.config.js,配置如下:
const config = {
  // 单页面导入
  entry:'./src/javascript/index.js',//或者entry:['./src/javascript/index.js','./src/javascript/print.js']结果也一样,只是index和print代码合并了

  // 多页面导入
  // entry:{
  //   index:'./src/javascript/index.js',
  //   print:'./src/javascript/print.js',
  // }
  // 输出的文件会是两个index.js和print.js

  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'javascript/[name].js',
  },
}
```
**多页面应用打包通用方案**
```bash
# 多页面目录
# 一个页面必须是一个文件夹,而且文件夹里的html和js名称必须是index
src
  index
    index.html
    index.js
  print
    index.html
    index.js
```
```js
// 多页面应用打包设置
// plugin:glob
const glob = require('glob');
const NODE_ENV =
// 多页面应用打包函数
const setMpa = ()=>{
  const entry = {};
  const htmlWebpackPlugins = [];
  const entryFiles = golb.sync(path.join(__dirname,'dist'));

  Object.keys(entryFiles).map((index) => {
    const entryFiles = entryFiles[index];
    const match = entryFiles.match(/src\/(.*)\/index\.js/);
    const pageName = match && match[1];

    entry[pageName] = entryFiles;

    htmlwebpackplugins.push(
      new htmlwebpackplugin({
        template:path.resover(__dirname,`src/${pageName}/index.html`),
        filename:`${pageName}`,
      });

      return {
        entry,
        htmlwebpackplugins
      };
    );

  });

};
const {entry,htmlwebpackplugins} = setMpa();

// webapck config
module.exports = {
  entry: entry,
  plugins:[

  ].concat(htmlwebpackplugins),
}

// 多页面打包到dist目录下的结果
// dist
//    index.html
//    print.html
```



## webapck打包组件和库
自己编写包时需要一个全新的环境(新建一个项目)
```js
// print.js
export default function (a,b){
  // code
  // 自己编写包,需要执行什么样的功能的源代码
  }
}

// webapck.config.js
// plugin:terser-webpack-plugn
module.exports = {
  entry:{
    'print':'./src/print.js',
    'print.min':'./src/print.js',
  },
  output:{
    filename:'[name].js',
    library:'libraryName',//向外曝露print.js文件库的名称
    libraryTarget:'umd',//外部引用的方法,umd、commonjs、AMD等
    libraryExport:'default',
  },
  mode:'none',
  optimization:{
    minimize:true,//开启压缩
    minimizer:[//压缩匹配
      new TerserPlugn({
        include:/\.min\.js$/,
      })
    ]
  }
}

// index.js
if(process.env.NODE_ENV = 'production'){
  module.exports = require('./dist/print.min.js')
}else{
  module.exports = require('./dist/print.js')
}

// 修改pack.json
// description(项目描述)
// script下加一个打包执行指令如:
// "print":"webapck"  //指令可根据自己写的包情况而定
// 最后执行npm pulish发布到npm上(有npm账号才可以发布)
```



## 命令行信息显示优化
```js
// 统计信息stats
// errors-only:none; 只有发生错误时输出
// minimal:none;     只有发生错误和新的编译时输出
// none:false;       没有输出
// normal:true       标准输出
// verbose:none      全部输出
module.exports = {
  //...
  stats: {
    // 未定义选项时，stats 选项的备用值(fallback value)（优先级高于 webpack 本地默认值）
    all: undefined,

    // 添加资源信息
    assets: true,

    // 对资源按指定的字段进行排序
    // 你可以使用 `!field` 来反转排序。
    // Some possible values: 'id' (default), 'name', 'size', 'chunks', 'failed', 'issuer'
    // For a complete list of fields see the bottom of the page
    assetsSort: "field",

    // 添加构建日期和构建时间信息
    builtAt: true,

    // 添加缓存（但未构建）模块的信息
    cached: true,

    // 显示缓存的资源（将其设置为 `false` 则仅显示输出的文件）
    cachedAssets: true,

    // 添加 children 信息
    children: true,

    // 添加 chunk 信息（设置为 `false` 能允许较少的冗长输出）
    chunks: true,

    // 添加 namedChunkGroups 信息
    chunkGroups: true,

    // 将构建模块信息添加到 chunk 信息
    chunkModules: true,

    // 添加 chunk 和 chunk merge 来源的信息
    chunkOrigins: true,

    // 按指定的字段，对 chunk 进行排序
    // 你可以使用 `!field` 来反转排序。默认是按照 `id` 排序。
    // Some other possible values: 'name', 'size', 'chunks', 'failed', 'issuer'
    // For a complete list of fields see the bottom of the page
    chunksSort: "field",

    // 用于缩短 request 的上下文目录
    context: "../src/",

    // `webpack --colors` 等同于
    colors: false,

    // 显示每个模块到入口起点的距离(distance)
    depth: false,

    // 通过对应的 bundle 显示入口起点
    entrypoints: false,

    // 添加 --env information
    env: false,

    // 添加错误信息
    errors: true,

    // 添加错误的详细信息（就像解析日志一样）
    errorDetails: true,

    // 将资源显示在 stats 中的情况排除
    // 这可以通过 String, RegExp, 获取 assetName 的函数来实现
    // 并返回一个布尔值或如下所述的数组。
    excludeAssets: "filter" | /filter/ | (assetName) => true | false |
      ["filter"] | [/filter/] | [(assetName) => true|false],

    // 将模块显示在 stats 中的情况排除
    // 这可以通过 String, RegExp, 获取 moduleSource 的函数来实现
    // 并返回一个布尔值或如下所述的数组。
    excludeModules: "filter" | /filter/ | (moduleSource) => true | false |
      ["filter"] | [/filter/] | [(moduleSource) => true|false],

    // 查看 excludeModules
    exclude: "filter" | /filter/ | (moduleSource) => true | false |
          ["filter"] | [/filter/] | [(moduleSource) => true|false],

    // 添加 compilation 的哈希值
    hash: true,

    // 设置要显示的模块的最大数量
    maxModules: 15,

    // 添加构建模块信息
    modules: true,

    // 按指定的字段，对模块进行排序
    // 你可以使用 `!field` 来反转排序。默认是按照 `id` 排序。
    // Some other possible values: 'name', 'size', 'chunks', 'failed', 'issuer'
    // For a complete list of fields see the bottom of the page
    modulesSort: "field",

    // 显示警告/错误的依赖和来源（从 webpack 2.5.0 开始）
    moduleTrace: true,

    // 当文件大小超过 `performance.maxAssetSize` 时显示性能提示
    performance: true,

    // 显示模块的导出
    providedExports: false,

    // 添加 public path 的信息
    publicPath: true,

    // 添加模块被引入的原因
    reasons: true,

    // 添加模块的源码
    source: false,

    // 添加时间信息
    timings: true,

    // 显示哪个模块导出被用到
    usedExports: false,

    // 添加 webpack 版本信息
    version: true,

    // 添加警告
    warnings: true,

    // 过滤警告显示（从 webpack 2.4.0 开始），
    // 可以是 String, Regexp, 一个获取 warning 的函数
    // 并返回一个布尔值或上述组合的数组。第一个匹配到的为胜(First match wins.)。
    warningsFilter: "filter" | /filter/ | ["filter", /filter/] | (warning) => true|false
  }
}

// 推荐
// 优化命令行的构建日志
// plugin:friendly-errors-webpack-plugin
// 比webpack自带的更为有好些
const friendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');

module.exports = {
  //...
  plugins:[
    new friendlyErrorsWebpackPlugin()
  ]
  stats: 'errors-only',//设置只有报错时提示
}
```



## 错误捕获和处理


## html和js内联
主要解决html和js中小段代码内联到html中,如:每个html页面中需要大量的`<meta>`信息,每个页面都需要,就可以使用内联.
laoder:raw-loader
```html
<!-- html中内联的html和js片段 -->
<!-- 内联的的html片段 -->
<script>${require('raw-loader!babel-loader!./meta.html')}</script>

<!-- 内联的的js片段 -->
<script>${require('raw-loader!babel-loader!./node_modules/lib')}</script>
```
```js
// css内联
// laoder:style-loader、html-inline-css-webpack-plugin raw-loader
// webpack.config.js
modules:{
  rules:[
    {
      test:/\.s[ac]ss$/,
      use:[
        {
          loader:'style-loader',
          options:{
            insertAt:'top',//样式插入到<head>
            singleton:true,//将所有的style标签合并成一个
          }
        },
        {loader:'css-loader',options:{sourceMap:true}},
        {loader:'sass-loader',options:{sourceMap:true}},
      ],
    },
  ],
}
```



## webpack打包配置优化
```js
// 在项目目录中创建libs文件夹,创建webpack.base.js、webpack.dev.js、webpack.pro.js三个js文件
// webpack.base.js:提取webpack.config.js开发环境和生产环境的共同配置
// webpack.dev.js:开发环境中的配置
// webpack.pro.js:生产环境中的配置


// loader:webapck-merge
// 在webpack.base.js文件中开发环境和生产环境公共配置。
const { CleanWebpackPlugin } = require('clean-webpack-plugin'); //清理目录
const ExtractTextPlugin = require('extract-text-webpack-plugin'); //css模块化插件
const MiniCssExtractPlugin = require("mini-css-extract-plugin"); // 将css代码提取为独立文件的插件
const HtmlPlugin = require('html-webpack-plugin'); // 用于生成html文件的插件

module.exports = {
  module: {
    rule:[
      // ······
    ],
  },
  plugins: [
    new CleanWebpackPlugin(), //清理目录
    new MiniCssExtractPlugin({ filename: "css/[name].css" }), //提取css为单独文件
  ],

  // ······

};

// 在webpack.dev.js文件中开发环境配置。
const path = require('path');
const webpack = require('webpack'); //访问内置的插件
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base'); //webpack公共配置

let devConfig = {
  entry:'./src/javascript/index.js',
  output: {
    path: path.resolve(__dirname, '../build'), //build文件夹为测试使用,dist为上线使用,
    filename: 'javascript/[name].js',
  },
  module:{
    // rules ······
  },
  plugins:[
    // plugins ······
  ],

  devtool: 'source-map',
  mode: 'development'
};

module.exports = merge(baseConfig, devConfig);


// 在webpack.pro.js文件中生产环境配置。
const path = require('path');
const webpack = require('webpack'); //访问内置的插件
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base'); //webpack公共配置

let proConfig = {
  entry:'./src/javascript/index.js',
  output: {
    path: path.resolve(__dirname, '../dist'), //build文件夹为测试使用,dist为上线使用,
    filename: 'javascript/[name].js',
  },
  module:{
    // rules ······
  },
  plugins:[
    // plugins ······
  ],
  devtool: false,
  mode: 'production'
};

module.exports = merge(baseConfig, proConfig);
```

