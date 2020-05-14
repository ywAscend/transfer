---
title: use
date: 2020-05-12 13:13:34
tags:
---

webpack四个核心概念

1. 入口：entry
```
 webpack入口，指示webpack应该使用哪个模块，来作为构建内部依赖图的开始。

 示例：
 module.exports = {
     entry: './src/index.js'
 }

```
2. 出口：output
```
webpack打包完成后，输出包。包含包名（filename）、输出包的位置(path)。

示例：
    const path = require('path')
    module.exports = {
        entry: './src/index',
        output:{
            filename: 'built.js',  //输出包名为 built.js
            path:path.resolve(__dirname,'build')  //输出包的位置为当前目录下的 build 文件夹下
        }
    }

```

3. loader
```
由于webpack自身只能处理js，但实际项目中会依赖以来很多非js类型的文件。而loader提供了webpack处理那些非js文件的能力。
loader可以将所有类型的文件转为webpack能够处理的有效模块。然后再通过webpack的打包能力，进行处理打包。
本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图可以直接引用的模块。

loader中通常由两个属性 test、use

-1) test 属性，用于标识要转换的文件类型
-2）use 属性，表示进行转换时，要使用的loader

示例：
    const path = require('path')
    module.exports = {
        entry: './src/index.js',
        output: {
            filename: 'built.js',
            path: path.resolve(__dirname,'build')
        },
        module: {  //loader模块
            rules: [  
                {   //css-loader
                    test: /\.css$/,  
                    use:['style-loader','css-loader']

                }
            ]
        }
    }

```

4. 插件 plugins
```
loader 被用于转换某些类型的模块，而插件可以用于执行范围更广的任务。是对loader能力的扩充。
插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能及其强大，可以用
处理各种各样的任务。

插件的使用步骤：
安装插件 --> 引用插件（require）--> 使用插件（通常为new一个示例，添加到plugins数组中）

示例：

    const path = require('path')
    //引入插件 html-webpack-plugin
    const HtmlWebpackPlugin = require('html-webpack-plugin')

    module.exports = {
        entry: './src/index.js',
        output: {
            filename: 'built.js',
            path: path.resolve(__dirname,'build')
        },
        module:{
            rules: [
                {
                    test: /\.css$/,
                    use:['style-loader','css-loader']
                }
            ]
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: './src/index.html'
            })
        ]
    } 

```

5. 模式 mode

```
mode 参数 可选值为 development 或 production，webpack会根据相应的模式进行内置优化

development: 开发模式，代码不会压缩
productionL: 生产模式，webpack自动压缩代码

```

#### 以上全部简单示例
```
 const path = require('path')
    //引入插件 html-webpack-plugin
    const HtmlWebpackPlugin = require('html-webpack-plugin')

    module.exports = {
        entry: './src/index.js',
        output: {
            filename: 'built.js',
            path: path.resolve(__dirname,'build')
        },
        mode: 'development',
        module:{
            rules: [
                {
                    test: /\.css$/,
                    use:['style-loader','css-loader']
                }
            ]
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: './src/index.html'
            })
        ]
    } 
```