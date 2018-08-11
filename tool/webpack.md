# webpack
> webpack

## 概念
![alt](./imgs/webpack-1.png)

不像大多数的模块打包机，webpack是把项目当作一个整体，通过一个给定的的主文件，webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包成一个或多个浏览器可识别的js文件

## install
首先添加我们即将使用的包：
```
npm install webpack webpack-dev-server --save-dev
```
webpack是我们需要的模块打包机，webpack-dev-server用来创建本地服务器，监听你的代码修改，并自动刷新修改后的结果。这些是有关devServer的配置
```
contentBase,  // 为文件提供本地服务器
port, // 监听端口，默认8080
inline, // 设置为true,源文件发生改变自动刷新页面
historyApiFallback  // 依赖HTML5 history API,如果设置为true,所有的页面跳转指向index.html
devServer:{
    contentBase: './src' // 本地服务器所加载的页面所在的目录
    historyApiFallback: true, // 不跳转
    inline: true // 实时刷新
}
```
然后我们在根目录下创建一个'webpack.config.js'，在'package.json'添加两个命令用于本地开发和生产发布
    
```
"scripts": {
  "start": "webpack-dev-server",
  "build": "webpack"
}
```
在使用webpack命令的时候，他将接受webpack的配置文件，除非我们使用其他的操作

## entry
entry: 用来写入口文件，它将是整个依赖关系的根
```js
var baseConfig = {
   entry: './src/index.js'
}
```
当我们需要多个入口文件的时候，可以把entry写成一个对象
```js
var baseConfig = {
   entry: {
      main: './src/index.js'
   }
}
```

## output
output：即使入口文件有多个，但是只有一个输出配置
```js
var path = require('path')
var baseConfig = {
   entry: {
      main: './src/index.js'
   },
    output: {
       filename: 'main.js',
       ath: path.resolve('./build')
    }
}
module.exports = baseConfig
```
如果你定义的入口文件有多个，那么我们需要使用占位符来确保输出文件的唯一性
```js
output: {
  filename: '[name].js',
  path: path.resolve('./build')
}
```
如今这么少的配置，就能够让你运行一个服务器并在本地使用命令npm start或者npm run build来打包我们的代码进行发布

## Loader
loader的作用： 
1. 实现对不同格式的文件的处理，比如说将scss转换为css，或者typescript转化为js
2. 转换这些文件，从而使其能够被添加到依赖图中

loader是webpack最重要的部分之一，通过使用不同的Loader，我们能够调用外部的脚本或者工具，实现对不同格式文件的处理，loader需要在webpack.config.js里边单独用module进行配置，配置如下：
```js
    test: 匹配所处理文件的扩展名的正则表达式（必须）
    loader: loader的名称（必须）
    include/exclude: 手动添加处理的文件，屏蔽不需要处理的文件（可选）
    query: 为loaders提供额外的设置选项
    ex: 
        var baseConfig = {
            // ...
            module: {
                rules: [
                    {
                        test: /*匹配文件后缀名的正则*/,
                        use: [
                            loader: /*loader名字*/,
                            query: /*额外配置*/
                        ]
                    }
                ]
            }
        }
```
要是loader工作，我们需要一个正则表达式来标识我们要修改的文件，然后有一个数组表示
我们表示我们即将使用的Loader,当然我们需要的loader需要通过npm 进行安装。例如我们需要解析less的文件，那么webpack.config.js的配置如下：
```js
        var baseConfig = {
                entry: {
                    main: './src/index.js'
                },
                output: {
                    filename: '[name].js',
                    path: path.resolve('./build')
                },
                devServer: {
                    contentBase: './src',
                    historyApiFallBack: true,
                    inline: true
                },
                module: {
                    rules: [
                        {
                            test: /\.less$/,
                            use: [
                                {loader: 'style-loader'},
                                {loader: 'css-loader'},
                                {loader: 'less-loader'}
                            ],
                            exclude: /node_modules/
                        }
                    ]
                }
            }
```
这里介绍几个常用的loader：
* babel-loader： 让下一代的js文件转换成现代浏览器能够支持的JS文件。babel有些复杂，所以大多数都会新建一个.babelrc进行配置
* css-loader,style-loader:两个建议配合使用，用来解析css文件，能够解释@import,url()如果需要解析less就在后面加一个less-loader
* file-loader: 生成的文件名就是文件内容的MD5哈希值并会保留所引用资源的原始扩展名
* url-loader: 功能类似 file-loader,但是文件大小低于指定的限制时，可以返回一个DataURL事实上，在使用less,scss,stylus这些的时候，npm会提示差什么插件，差什么，安上就行了

## Plugins
**loaders负责的是处理源文件的如css、jsx，一次处理一个文件。而plugins并不是直接操作单个文件，它直接对整个构建过程起作用**

* ExtractTextWebpackPlugin: 它会将入口中引用css文件，都打包都独立的css文件中，而不是内嵌在js打包文件中
```js
    var ExtractTextPlugin = require('extract-text-webpack-plugin')
        var lessRules = {
            use: [
                {loader: 'css-loader'},
                {loader: 'less-loader'}
            ]
        }
        
        var baseConfig = {
            // ... 
            module: {
                rules: [
                    // ...
                    {test: /\.less$/, use: ExtractTextPlugin.extract(lessRules)}
                ]
            },
            plugins: [
                new ExtractTextPlugin('main.css')
            ]
        }
```
* HtmlWebpackPlugin:依据一个简单的index.html模版，生成一个自动引用你打包后的js文件的新index.html
```js
        var HTMLWebpackPlugin = require('html-webpack-plugin')
            var baseConfig = {
                // ...
                plugins: [
                    new HTMLWebpackPlugin()
                ]
            }
```
* HotModuleReplacementPlugin: 它允许你在修改组件代码时，自动刷新实时预览修改后的结果注意永远不要在生产环境中使用HMR。这儿说一下一般情况分为开发环境，测试环境，生产环境。

## 最后
### 什么是webpack和grunt和gulp有什么不同
> 答案：Webpack是一个模块打包器，他可以递归的打包项目中的所有模块，最终生成几个打包后的文件。他和其他的工具最大的不同在于他支持code-splitting、模块化(AMD，ESM，CommonJs)、全局分析。

### 什么是bundle,什么是chunk，什么是module?
> 答案：bundle是由webpack打包出来的文件，chunk是指webpack在进行模块的依赖分析的时候，代码分割出来的代码块。module是开发中的单个模块。