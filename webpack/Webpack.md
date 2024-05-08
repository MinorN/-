# Webpack 5

## 配置

默认文件名：`webpack.config.js`，如果需要使用其他的配置文件，可以使用 `--config` 修改 Eg：`webpack --config ./webpack.dev.js`

### context

webpack 工作上下文，用来解析 entry 和 loader，有两种配置方式，默认为当前 node 进程目录，也可以使用 path 设置绝对路径

```js
module.exports = {
    context: path.resolve(__dirname,'src')
}
```

### entry

打包入口，可以是一个，也可以是多个，有多种配置方式

```js
module.exports = {
    // 第一种
    entry: './src/index.js'
    // 第二种
    entry: ['./src/index.js','./sr/index2.js']
    // 第三种，适合分别打包
    entry:{
        app:{
            import: './src/index.js', // 入口
            dependOn: ['react-vendors'], // 需要的依赖单独打包
            filename: 'app.js', // 打包文件名称
        },
        'react-vendors':'react',
    }
}
```

### output

出口，可以配置输出位置以及如何输出

```js
module.exports = {
    output: {
        path: path.resolve(__dirname,'build'), // 输出的位置，绝对路径
        publicPath: 'https://xxx.xx/static/', // 按需加载或者外部资源的真实路径，默认为相对路径,一般可以搭配cdn使用
        crossOriginLoading: false,// 是否跨域，默认为 false  false | anonymous | use-credetials 
        filename: 'build.js', // 输出文件名
        assetModuleFilename: 'asset', // 静态资源输出的名称
        chunkFilename: 'asset_[id].js', //非初始chunk文件名称 [id] [name] [file] [base] [path]
        library: {
            name: 'test-library',
            type: 'var', // 库暴漏的方式 var module umd cmd require
            export: , // 指定哪个导出应该为一个库
        }, // 导出库配置
    }
}
```

### loader

> webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)，以供应用程序使用，以及被添加到依赖图中。

loader 可以支持其他类型资源的编译，比如：`css`、`html`等，有两种使用方式

1. 在 `config` 里面设置每种资源对应的 `loader`
2. 在对应的引用语句中设置对应的 `loader`，一般不使用该方法

常见的`loader`

* babel-loader
* ts-loader
* css-loader
* sass-loader
* style-loader

```js
module.exports = {
    module:{
        rules:[
            {
                test: /\.css$/, // 匹配文件
                loader:'css-loader', // 使用单个loader
                use: ['sass-loader','css-loader'], // 使用多个loader,顺序是从右往左
            },
        ]
    }
}
```

### plugin

> loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

```javascript
module.exports = {
    plugins: []
    // 有顺序
}
```

常用插件

* HtmlWebpackPlugin： html 解析
* ESLintWebpackPlugin：eslint 检测
* MiniCssExtractPlugin：样式文件分割
* TerserPlugin：js压缩
* CssMinimizerPlugin：css压缩
* PrefetchPlugin：代码预加载

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    plugins:[
        new HtmlWebpackPlugin({
            title: '测试', // html 的 title
            filename: 'html-filename.html', // 输出的文件名
            template: path.resolve(__dirname,'./src/index.html'), // 指定 html 模板
            templateParameters: {
                titleName: '测试2'
            } , // 替换模板中的数据，需要使用 ejs 语法来进行接收
            publicPath: 'https://xxx.xx/static/', //  按需加载或者外部资源的真实路径
            minify: {
                ...
            }, // 压缩html
        })
    ]
}
```

```javascript
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')
module.exports = {
    optimization:{
        minimizer:[
			new CssMinimizerPlugin({
                test: /index\.css/, // 指定匹配文件，匹配的是结果生成的文件,这里指的是只压缩index.css 这个文件
                include: [], // 包含文件
                exclude: [], // 排除文件
                parallel: true, // 是否启用多进程
                minify: CssMinimizerPlugin.cssnanoMinify, // 指定使用哪个库来进行压缩 默认使用 cssnanoMinify,还有其他的
                minimizerOptions: { preset: 'default' }, // cssnano 优化选项，每个库不一样
            })
        ]
        // 这里是 CssMinimizerPlugin 使用的地方
    }
}
```

### mode

> 通过选择 `development`, `production` 或 `none` 之中的一个，来设置 `mode` 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 `production`

* development：开发模式，一般不会进行分包、压缩代码等操作
* production：生产模式
* none：默认模式

```javascript
module.exports = {
	mode: 'development', // 一般是通过 node 的 env 来进行获取修改
   	// mode: process.env
}
```

有时候，我们也会通过命令行来改变模式

```json
"scripts":{
    "dev": "webpack --mode=development",
    "prod": "webpack --mode=production",
}
```

### sourcemap

sourcemap 一般是用来开发、调试bug

方式：`[inline/hidden/eval]-[nosources]-[cheap-[module]]-source-map` `[]`表示可选项内容

引入方式： inline、hidden、eval

源码展示：nosources

调试展示：cheap、cheap-module

```javascript
module.exports = {
    devtool: 'source-map'
}
```

### devserver

首先得安装 `devserver`

```bash
npm i webpack-dev-server
```

修改启动设置

```json
"scripts":{
	"build": "webpack server"
}
```

具体配置

```javascript
module.exports = {
    devserver: {
        client: {
            overlay: false, // 出错时是否页面也提示
        },
        // 配置代理
        proxy: {
            '/api': 'http:localhost:3000', // 目标地址
            '/xxx/*': { // 代表 /xxx/底下所有的请求
                target: 'http:localhost:3000',
                pathRewrite:{
                    '^/xxx':'' // 把xxx去掉
                },
                bypass:(req,res,proxyOptions) =>{
                    // 针对某些特殊情况进行处理
                    if(req.url.indexOf('test') !== -1){
                        return '/'
                    }else{
                        ...
                    }
                    },
                },
                secure: false, // 是否必须使用 https
            },
            compress: false, // 是否启用 gzip 压缩
            host: '0.0.0.0', // 可被外部访问
            hot: 'only', // 是否自动刷新浏览器
            open: true, // 是否打开新的浏览器
            port: 8080, // 默认端口
            server: 'https', // 设置访问的协议
            static: ['assets'], // 静态资源访问
            publicPath: '/static', // 同上publicPath
        },
    }
}
```

### 常见静态资源处理

**asset module**是一种允许用户使用`assets`文件并且无需额外配置加载器的`module`

在webpack5之前使用loader来进行处理，比如`raw-loader`（以字符串形式导入文件），`url-loader`（把文件作为 data url 内联）,`file-loader`（将文件发送到输出目录），在webpack5则内置了该功能，分别对应为`asset/source`,`asset/inline`,`asset/resource`

```javascript
module.exports = {
    module:{
        rules:[
            {
                test: /\.txt$/,
                type: 'asset/source'
            },
            {
                test: /\.xlsx$/,
                type: 'asset/resource'
            },
        ]
    }
}
```

会有这样场景：图片小的直接使用内联方式，大的使用文件方式，在webpack以前可以使用 url-loader实现，webpack5 内置了

```javascript
// webpack 以前版本
module.exports = {
    module:{
        rules:[
            {
                test: /\.jpeg/,
                type: 'asset',
                parser: {
                    dataUrlCondition:{
                        maxSize: 8 * 1024 // 8kb 以下内联
                    }
                }
            },
        ]
    }
}
```

## loader 执行过程

loader 分为几种类型

* pre loader
* normal loader
* post loader
* inline loader

可以在配置loader时，enforce进行相应的配置(`'pre'|''|'post'`)

loader由两部分组成，一部分是 pitch 函数，优先调用，具有熔断作用，另一部分则是 loader fn ，就是用来进行处理

```javascript
function loader(){}
loader.pitch = function(){}

module.exports = loader
```

如果有多个loader，那么会先调用每个loader的pitch方法，然后在调用每个loader fn

> loader1-pitch -> loader2-pitch -> loader3-pitch -> loader3 -> loader2->loader1

## 实现一个简易的 style oader

```javascript
// 先修改 webpack config 配置
module.exports = {
    resolveLoader: {
        alias: {
            loader1: path.resolve(__dirname, './loader/loader1.js'),
        },
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['loader1', 'css-loader'],
            },
        ],
    },
}
```

```js
// loader1.js
const { stringifyRequest } = require('loader-utils');
// 注意 loader-utils 版本
function loader(source) {

    return source
}

// remainingRequest 当前loader 执行后，还有哪些 loader 未执行
// previousRequest 当前loader 执行后, 已经执行的 loader
// data 数据
loader.pitch = function (remainingRequest, previousRequest, data) {
    // 拿到路径
    const modulePath = stringifyRequest(this, `!!${remainingRequest}`);
    // 插入到头部
    return `
    var element = document.createElement('style')
    var content = require(${modulePath})
    content = content.__esModule ? content.default : content
    element.innerHtml = content
    var parentEle = document.querySelector('head')
    parentEle.appendChild(element)
  `;
};

module.exports = loader;

```

## plugin 执行过程

1. 初始化对应实例
2. 调用apply，传入 compiler
3. 通过 compiler 进行自定义构建

常用的hooks

* entryOption：读取配置文件的entry，遍历入口文件
* run：准备构建
* compile：开始一次构建，准备创建
* buildModule：模块构建前触发，可以修改模块参数
* seal：构建完成时触发
* optimize：优化开始时触发

### 实现一个 plugin

## autoprefix

使用`autoprefix`来兼容各个浏览器样式，每个浏览器都会有不同的适配，所以在一些属性上需要添加前缀，`autoprefix`则是来做这件事的

> -moz：firefox 私有属性， -ms IE私有， -webkit chrome、safari私有 -o opera私有

使用 postcss-loader + autoprefixer 来进行自动添加

```js
{
    loader:'postcss-loader',
        options:{
            postcssOptions:{
                plugins: [
                    'autoprefixer',
                    {
                        browsers: [
                            'last 10 Chrome versions',
                            'last 5 Firefox versions',
                            'Safari >= 6', 
                            'ie> 8
                        ] 
                    }
                ]
            }
        }
}
// 也可以加到 package.json(browserslist) 或者 .browserlistrc 文件
```

## rem 与 px 转换

> rem 是一个相对单位，是相对于html根节点字体大小，比如根节点字体大小为16px，那么 1rem = 16px

引入 `amfe-flexible`来计算根元素字体大小，然后配置`postcss-pxtorem`来进行自动转化

```js
['postcss-pxtorem',{
    rootValue:75, // 基准值，宽度/10
    propList:['*'], // 匹配哪些属性去进行转换
}]
// 如果有些特殊的地方不想进行转换 可以写作 12Px
```

示例：

```js
// index.js
import 'amfe-flexible'

// webpack config
module.exports = {
 module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'css-loader',
          {
            loader: 'postcss-loader',
            options: {
              postcssOptions: {
                plugins: [
                  ['autoprefixer'],
                  ['postcss-pxtorem', { rootValue: 75, propsList: ['*'] }],
                ],
              },
            },
          },
        ],
      },
    ],
  }, 
}
```

## Code spliting

`Code spliting` 代码分割，不是所有的内容都会在首屏进行加载，做成按需加载可以提高首屏访问速度

* 可以设置多个入口
* 也可以依赖共享
* 也可以使用 import 动态导入模块
* 使用内置的 SplitChunksPlugin 属性

这里主要介绍 `SplitChunksPlugin `对应配置

* chunks： 'async' | 'initial' | 'all'
* minSize：打包最小文件大小限制
* minChunks：最少共享chunks数，默认为1，比如一个模块被引用多次，可以将其单独打包

```js
module.exports = {
    optimization: {
        splitChunks: {
            chunks: 'all',
            minChunks: 1,
            minSize: 10000,
        },
    },
}
```

## 公共静态资源提取

* 使用外链：减少了打包体积
  * 直接写在html中
  * 使用html-webpack-plugin Eg：`<% for(var item in list) {%><script src="<%=list[item]%>"></script><%}%>`
  * 使用 webpack externals

```js
// 写在 HTML 中
module.exports = {
    externals: {
        vue1: 'vue'
        // 在页面 import 的时候要使用 vue1
    }
}
// 使用 webpack externals
module.exports = {
    externalsType:'script',
    externals:{
        vue1 : ['http:xxx.com/vue.min.js','vue']
    }
}

```

## 资源内联`html`中

在页面加载过程中，如果`js，css`是从外部加载，那么会造成阻塞，得等到j`s、css`加载完成后页面才能继续加载，导致加载变慢

* `html`片段：`raw-loader` 或者 `asset/source + html-webpack-plugin`

```js
module.exports = {
    module:{
        rules:[
            {
                test:/inline\/.*\.html/,
                type:'asset/source'
            }
        ]
    }
}
```

* js：和`html`片段一样
* `css`：`style-loader`

## 多页面打包

需要区分多个入口`entry`，以及多个 `HtmlWebpackPlugin`

```js
module.exports = {
    entry:{
        index:{
            import: './src/index/index.js',
            filename:'index.js'
        },
        list:{
            import: './src/list/index.js'
            filename:'list.js'
        }
    },
    plugins:[
        new HtmlWebpackPlugin({
            title: 'index', // html 的 title
            filename: 'html-filename.html', // 输出的文件名
            template: path.resolve(__dirname,'./src/index/index.html'), // 指定 html 模板
            templateParameters: {
                titleName: 'index html'
            } , // 替换模板中的数据，需要使用 ejs 语法来进行接收
            publicPath: 'https://xxx.xx/static/', //  按需加载或者外部资源的真实路径
            minify: {}, // 压缩html
            chunks:['index'], // 允许哪些chunk被加载
        }),
        new HtmlWebpackPlugin({
            title: 'list', // html 的 title
            filename: 'html-filename.html', // 输出的文件名
            template: path.resolve(__dirname,'./src/list/index.html'), // 指定 html 模板
            templateParameters: {
                titleName: 'list html'
            } , // 替换模板中的数据，需要使用 ejs 语法来进行接收
            publicPath: 'https://xxx.xx/static/', //  按需加载或者外部资源的真实路径
            minify: {}, // 压缩html
            chunks:['list'], // 允许哪些chunk被加载
        })
    ]
}
```

如果chunks过多，人为手写会出错误，所以需要自动生成配置，需要`glob`

```bash
npm i glob -D
```

```js
import glob = require('glob')
module.exports = (env,args) => {
    // 找到入口文件
    const entryFiles = glob.sync('./src/**/index.js')
    const entries = []
    const HtmlWebpackPlugin = []
    entryFiles.forEach((file) => {
        const res = file.match(/\.\/src\/(.*)\/index.js/)
        entries[res[1]] = {
            import: res[0]
        }
        HtmlWebpackPlugin.push(new HtmlWebpackPlugin({
            title: res[1], // html 的 title
            filename: `${res[1]}.html`, // 输出的文件名
            template: path.resolve(__dirname,`./src/${res[1]}/index.html`), // 指定 html 模板
            templateParameters: {
                titleName: 'list html'
            } , // 替换模板中的数据，需要使用 ejs 语法来进行接收
            publicPath: 'https://xxx.xx/static/', //  按需加载或者外部资源的真实路径
            minify: {}, // 压缩html
            chunks:[res[1]], // 允许哪些chunk被加载
        }))
    })
    config.entry = entries
    config.plugins = HtmlWebpackPlugin.concat(config.plugins)
    return config
}
```

## Tree-Shaking 

`Tree-Shaking`又称摇曳树，目的是为了去除`dead-code`，减少代码体积，同时不影响功能

由于`ES6`的模块依赖关系是确定的，和运行时状态无关，所以可以通过静态分析来对代码进行分析。标记是否有副作用：副作用简单来说就是会对代码、环境产生影响，默认所有文件都是true，在 `package.json`中配置 `sideEffects: true`，也可以指定某些文件`sideEffects: [./index/index.js]`,`optimization.usedExports`用来标记导出的模块是否被使用，在生产环境默认为 true，`optimization.concatenateModules`，标记根据模块的依赖关系集合到一个模块（比如一个文件依赖其余两个文件，就会把这三个文件合在一起），然后 **terser** 就可以删除无用代码

## 自动清理前一次构建产物

使用 webpack 配置 output配置/clean-webpack-plugin

```js
const { CleanWebpackPlugin } = require(clean-webpack-plugin)
module.exports = {
    plugins: [
        new CleanWebpackPlugin()
    ],
    output:{
        clean: true
    }
}
```

## 去除调试日志

* 删除console

```js
new TerserPlugin({
    terserOptions: {
        compress: {
            drop_console: true, // 在 production 模式下生效
            drop_debugger: true,
        }
    }
})
```

## webpack 配置 eslint 检测

配置 .eslintrc.js，如何在构建过程中检查 eslint

在 webpack5 之前使用 webpack-loader 来进行检查，在 webpack5 使用 eslint-webpack-plugin 

```js
module.exports = {
    plugins:[
        new ESlintWebpackPlugin({
            fix: true
        })
    ]
}
```

## [module federation](https://webpack.docschina.org/concepts/module-federation/)

> 多个独立的构建可以组成一个应用程序，这些独立的构建之间不应该存在依赖关系，因此可以单独开发和部署它们。这通常被称作微前端，但并不仅限于此。

​	APP1 <-> **SHARE SCOPE** <-> APP2

常用配置

* name：应用名称
* library：应用名称、导出类型
* filename：文件名
* exposes：暴漏的模块名和路径
* remotes：依赖的模块名和路径
* shared：共享依赖（一般来说第三方库，要求版本也一致）

```js
const { ModuleFederationPlugin } = require('webpack').container

module.exports = {
    plugins:[
        new ModuleFederationPlugin({
            // 对应的配置
            name: 'index',
            library: {
                name: 'index',
                type: 'var'
            },
            filename: 'remote.js',
            exposes :{
                './button': './src/button/index.js',
            }
        })
    ]
}
```

## webpack 优化

优化并不是盲目优化，要有具体的方向以及分析标准，比如：当前构建速度如何？是否满足自己的需求？慢在哪里？webpack流程是怎么样的？

### 构建速度分析

使用`speed-measure-webpack-plugin`

```bash
npm i speed-measure-webpack-plugin -D
```

用法

```js
const SpeedMeasureWebpackPlugin = require('speed-measure-webpack-plugin')

const smwp = new SpeedMeasureWebpackPlugin()

module.exports = smwp.wrap({})
```

当然，webpack 也有自带的 `webpack --profile --json=analyse.json`

### 多核优化

使用`thread-loader`来开启多核

```bash
npm i thread-loader -D
```

主要是针对某个`loader`来进行使用

```js
module.exports = {
    module:{
        rules:[
            {
                test: /\.css$/,
                use:[{
                    loader:'thread-loader',
                    options:{
                        workers: 4
                    }
                },'style-loader','css-loader']
            }
        ]
    }
}
```

### 使用缓存提升二次构建速度

缓存：当文件出现变化时，针对变化的文件进行编译。先会去读取缓存，然后对比检查哪些出现变化，然后二次构建

缓存有两种模式：`memory`（存放在内存中），`filesystem`（放到磁盘中），当然也可以禁用缓存`cache:false`

memory 模式只在开发者模式下生效，并且默认使用该设置

```js
module.exports = {
    // cache: false 
    cache: {
        type: 'memory' | 'filesystem',  // filesystem 下，下面的配置会生效
        buildDependencies:{
            config: [__filename]
        }, // 构建额外依赖的代码
        cacheDirectory: './cache/webpack', // 缓存目录，默认为 node_modules/.cache/webpack
        name: 'myCache', // 缓存名称，为了创建隔离独立的缓存
        version: '1.0.0', // 缓存版本，不同版本无法使用
    }
}
```

### MF提升构建速度

MF 就是 [module federation](https://webpack.docschina.org/concepts/module-federation/)，一些公共模块基本处于不变状态，之后构建这部分不需要重新构建

### 使用外链来提升构建速度

直接使用`script`标签来引入，无需构建

### 提高Node版本以及Webpack

版本迭代的过程中也会对自身进行一定优化

### 使用 esbuild 提升构建速度

`esbuild` 是一个构建工具，由于esbuild没有AST过程，所以构建速度会相对较快，但是由于没有AST过程，也会导致部分plugin无法使用，所以需要权衡使用或者部分使用。那么如何再 webpack 中使用 esbuild？

```bash
npm i esbuild-loader -D
```

然后把 `babel-loader` 替换成 `esbuild-loader`

```js
use:[
    {
        loader:'esbuild-loader',
        options:{
            target:'es2015',
            loader:'jsx',
        }
    }
]
```

同样也提供了压缩功能

`CssMinimizerPlugin & TerserPlugin`可以替换成 `ESBuildMiniifyPlugin`

```js
new ESBuildMiniifyPlugin({
    target: 'es2015',
    legalComments: 'none', // 注释
    css: true
})
```

### 使用 swc 提升 webpack 构建速度

swc 是使用 Rust 写的高性能 TS/JS 转译器，类似于 babel

```bash
npm i swc-loader @swc/core -D
```
具体配置查看 [文档](https://swc.rs/)

```js
use:[
    {
        loader: 'swc-loader',
        options: {
            jsc: {
                parser: {
                    jsx: true
                }
            }
        }
    }
]
```

### 构建产物体积分析

在我们构建完成后，需要对构建后的产物进行分析，使用`webpack-bundle-analyzer`

```bash
npm i webpack-bundle-analyzer -D
```

使用

```js
new BundleAnalyzerPlugin({
    analyzerMode: 'static', // 将结果输出为html文件
    openAnalyzer: true, // 打包完成后自动打开页面
})
```

### 减少构建产物体积

#### 代码压缩

1. html 压缩：`html-webpack-plugin` `minify`
2. css：`css-minimizer-webpack-plugin`
3. js：`terser-plugin`
4. js & css： `ESBuildMinifyPlugin`

#### 优化图片

1. 压缩图片
2. CDN

webpack配置： `image-minimizer-webpack-plugin`，`imagemin`，图片压缩也分为两种：无损压缩和有损压缩，分别有对应的插件

```bash
npm i image-minimizer-webpack-plugin imagemin imagemin-jpegtran -D
```

```js
new ImageminimizerPlugin({
    minimizer:{
        implementation: ImageminimizerPlugin.imageminGenerate,
        options:{
            plugins: [
               [ 'jpegtran', {progressive:true}]
            ]
        }
    }
})
```

#### 清除无用代码

常见的无用代码：

* 调试代码，在之前介绍过，在Terser里面进行配置
* 代码注释，也是在terserOptions里面配置

```js
terserOptions:{
    output:{
        comments: false,
    },
    format:{
        comments: false,
    }
    // 这两个配置 二选一
},
extractComments: false
```

* 无用变量、函数，tree-shaking

#### 分包

* import 异步引入
* entry 声明
* splitChunks

重点介绍`splitChunks`

```js
chunks: all | async | initial // 分包模式
minSize: 20000 // chunk的最小体积
minRenmainingSize: 0 // 确保拆分后剩余的最小chunk体积
minChunks: 1 // 共享模块最小chunk数，意思是被引用多少次才会被拆分
maxAsyncRequests: 30 // 按需加载时最大并行请求数
maxInitialRequests: 30 // 入口点的最大并行请求数
cacheGroups:{
    
} // 可以继承或者覆盖
```

首先，chunks这三个分包模式有什么区别？

* async：只有按需引入的模块会被优化，默认为该模式
* initial：同步、异步引入都会优化，异步引用中的引用不会分包，也就是异步嵌套不会分包
* all：综合上两者

其次，cacheGroups

```js
test: // 匹配模块
priority: -10 // 权重，比如有同样模块，谁权重高谁为主
reuseExistingChunk: true // 如果当前chunk已经从主bundle中拆分，会被重用
```

#### 优化polyfill

`polyfill`是为了做一些代码兼容，就会产生一些额外的代码，可以针对浏览器不同来对`profill`来进行优化，比如某些低版本浏览器不需要兼容，可以去掉相关代码

* 如何使用 profill
  * import @babel/profill 或者 core-js
  * 结合@babel/preset-env
* 如何配置 useBuiltIns?
  * entry：结合targets，去掉目标浏览器已经支持的 profill 模块，但是需要手动引入
  * usage：不需要手动在代码中引入，打包会自动引用
  * false：不会自动引入 profill 模块

