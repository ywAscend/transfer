---
title: baseConfig
date: 2020-05-12 14:03:21
tags:
---
webpack common config

1. 入口

一、单个入口文件：
```
    module.exports = {
        entry: {
            main: './src/index.js'
        }
    }

```
单个入口文件简写：
```
    module.exports = {
        entry: './src/index.js'
    }

```
二、对象语法

```
    module.exports = {
        entry: {
            app: './src/index.js',
            vendors: './src/vendors.js'
        }
    }

```

常见应用场景：

分离应用程序和第三方库入口：

```
    const config = {
    entry: {
        app: './src/app.js',
        vendors: './src/vendors.js'
    }
    }
```
webpack 从 app.js 和 vendors.js 开始创建依赖图(dependency graph)。这些依赖图是彼此完全分离、互相独立的（每个 bundle 中都有一个 webpack 引导(bootstrap)）。这种方式比较常见于，只有一个入口起点（不包括 vendor）的单页应用程序(single page application)中。

此设置允许你使用 CommonsChunkPlugin 从「应用程序 bundle」中提取 vendor 引用(vendor reference) 到 vendor bundle，并把引用 vendor 的部分替换为 __webpack_require__() 调用。如果应用程序 bundle 中没有 vendor 代码，那么你可以在 webpack 中实现被称为长效缓存的通用模式。

多页面应用程序：

```
    const config = {
    entry: {
        pageOne: './src/pageOne/index.js',
        pageTwo: './src/pageTwo/index.js',
        pageThree: './src/pageThree/index.js'
    }
    }

```

上面的示例告诉 webpack 需要 3 个独立分离的依赖图.
在多页应用中，（译注：每当页面跳转时）服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很多事：
使用 CommonsChunkPlugin 为每个页面间的应用程序共享代码创建 bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益。

2. 出口  

配置 output 选项可以控制 webpack 如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个输出配置.

output 的值需要设置为一个对象，包含以下两点：

filename 用于输出文件的文件名。

目标输出目录 path 的绝对路径。

```
const path = require('path')
module.exports = {
    output: {
        filename: 'built.js',
        path: path.reslove(__dirname,'build')
    }
}

```
多个入口起点

如果配置创建了多个单独的 "chunk"（例如，使用多个入口起点或使用像 CommonsChunkPlugin 这样的插件），则应该使用占位符(substitutions)来确保每个文件具有唯一的名称。

```
    {
        entry: {
            app: './src/app.js',
            search: './src/search.js'
        },
        output: {
            filename: '[name].js',
            path: __dirname + '/dist'
        }
    }

    // 写入到硬盘：./dist/app.js, ./dist/search.js

```

使用CDN加速(官方示例，没太懂)

config.js 

```
    output: {
        path: "/home/proj/cdn/assets/[hash]",
        publicPath: "http://cdn.example.com/assets/[hash]/"
    }

```

在编译时不知道最终输出文件的 publicPath 的情况下，publicPath 可以留空，并且在入口起点文件运行时动态设置。如果你在编译时不知道 publicPath，你可以先忽略它，并且在入口起点设置 __webpack_public_path__。

3. loader

#### 处理样式 css/less/sass
```
const config = {
    module: {
        rules: [
            {
                test: /\.css$/,
                use:['style-loader','css-loader']
            },
            {
                test: /\.les$/,
                use:['style-loader','css-loader','less-loader']
            },
            {
                test: /\.(sass|scss)$/,
                use:['style-loader','css-loader','sass-loader']
            }
        ]
    }
}

module.exports = config
```

#### 处理图片 jpg|png|gif|jpeg

```
const config = {
    module: {
        rules: [
            {
                test: /\.(jpg|png|gif|jpeg)$/,
                loader: 'url-loader',
                options:{
                    limit: 8*1024, //小于8*1024的会被转为base64地址
                    name: '[name].[hash:10].[ext]', //name属性指向原来的名称，[hash:8]：取前10位hash值，总共32位
                    esModule: false, //关闭es6模块,使用commonjs模块
                    outputPath: 'imgs' //打包后图片资源的输出目录
                }
            },
            {
                //html中的图片资源
                test: /\.html$/,
                loader:'html-loader',
                options: {
                    outputPath:'imgs'
                }
            }
        ]
    }
}

module.exports = config
```

4. plugins

#### 使用html-webpack-plugin
将打包好的js文件自动注入到 html中

```
//安装
cnpm i html-webpack-plugin -D

//引用
const HtmlWebpackPlugin = require('html-webpack-plugin')

//使用

const config = {
    plugins:[
        new HtmlWebpackPlugin({
            template: './src/index.html'  //指定html模板
        })
    ]
}

```
5.  mode

配置中提供 mode 选项：
```
    module.exports = {
        mode: 'production'
    }

```
或者从 CLI 参数中传递：
```
    webpack --mode=production
```

6. devServer  配置本地服务器

Webpack提供了一个可选的本地开发服务器，这个本地服务器基于node.js构建，它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖：
安装插件 webpack-dev-server ： cnpm i webpack-dev-server -D

devServer作为webpack配置选项中的一项，以下是它的一些配置选项:

contentBase ：设置服务器所读取文件的目录，当前我们设置为"./build"
port ：设置端口号，如果省略，默认为8080
inline ：设置为true，当源文件改变时会自动刷新页面
historyApiFallback ：设置为true，所有的跳转将指向index.html

```
const webpack = require('webpack')
module.exports = {
    devServer:{
        contentBase: './bulid', //服务器运行目录，默认webpack-dev-server会为根文件夹提供本地服务器，
        compress: true, //启用gzip压缩
        port: 3000,  //端口
        open: true   //第一次运行时，自动打开浏览器
        //inline: true , //设置为true,源文件改变时自动刷新页面
        //historyApiFallback: true,  //设置为true，historyFallback能将所有没有做映射的地址都映射到一个入口：index.html中去。
        //hot: true   //热加载，功能：只渲染所改组件的页面效果，不会全部刷新，其他页面数据依然会存在。使用hot,需要配置 hotModule相关模块
    },
    plugins:[
        //热加载相关
        new webpack.NameModulesPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ]
}

```
7 .source Map配置   [具体配置详见](https://segmentfault.com/a/1190000008315937)

为方便调试打包后的文件，定位问题，通过配置source map 在打包时生成.map文件，使得打包后的代码可读性更高，更易于调试。

```
    const path = require('path')
    module.exports = {
        entry:'./src/index.js',
        output: {
            filename: 'built.js',
            path:path.reslove(__dirname,'build')
        },
        devtool: 'source-map' // 会生成对于调试的完整的.map文件，但同时也会减慢打包速度
    }
```

#### 基础配置汇总
```
    
    //引入path
    const Path = require('path')
    //引入 html-webpack-plugin
    const HtmlWebpackPlugin = require('html-webpack-plugin')

    module.exports = {
        entry: './src/js/index.js',
        output: {
            filename: 'built.js',
            path: Path.resolve(__dirname,'build')
        },
        //loader
        module:{
            rules:[
                //样式资源
                {
                    test: /\.css$/,
                    use:['style-loader','css-loader']
                },
                {
                    test: /\.less$/,
                    use:['style-loader','css-loader','less-loader']
                },
                //图片资源
                {
                    test: /\.(jpg|png|gif)$/,
                    loader: 'url-loader',
                    options:{
                        limit: 8*1024,
                        esModule:false,
                        name:'[name].[hash:10].[ext]',
                        outputPath: 'imgs'

                    }
                },
                {  //html图片资源
                    test: /\.html$/,
                    loader: 'html-loader',
                },
                //其他资源，字体图标
                {
                    //exclude:/\.(js|html|css|jpg|png|gif|less)$/,
                    test:/\.(woff2|eot|ttf|woff|svg)$/,
                    loader: 'file-loader',
                    options:{
                        outputPath:'media'
                    }
                }
            ]
        },
        mode: 'development',
        plugins:[
            new HtmlWebpackPlugin({
                template:'./src/index.html'
            })
        ],
        devServer:{
            contentBase: './build',
            compress: true,
            port: 3000,
            open: true
        },
        devtool: 'source-map'  // 会生成对于调试的完整的.map文件，但同时也会减慢打包速度
    }

```

