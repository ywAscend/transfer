---
title: css抽取为单独文件
date: 2020-05-13 16:06:41
tags:
---

基础配置中的css是通过style-loader 转为js的样式文件，最后插入在head中引入样式。这样的好处是，页面加载时不会重新请求css文件，但也有缺点页面一次性加载内容较多，影响加载速度。

```
    const config = {
        module:{
            rules:[
                test:/\.css$/,
                use:[
                    'style-loader', //创建style标签，将样式放入head中
                    'css-loader'
                ]
            ]
        }
    }

    module.exports = config

```
运行效果：
![]('./img/css1.png')

通过 mini-css-extract-plugin 插件，将打包后的css通过link引入
好处：页面不会闪白 弊端：会发生link请求

```
    //安装插件 
    cnpm i mini-css-extract-plugin
    //引入插件
    const MiniCssExtractPlugin = require('mini-css-extract-plugin')
    // 配置
    const config = {
        module:{
            rules:[
                // 'style-loader' ,
                MiniCssExtractPluign.loader, //这个loader取代style-loader。作用：提取js中的css成单独文件
                'css-loader'
            ]
        },
        plugins:[
            new MiniCssExtractPlugin({
                //对输出文件进行重命名
                filename: 'css/built.css'
            })
        ],
    }
```
运行效果：
![]('./img/css2.png')

完整配置 webpack.config.js：

```
    //引入路径
    const Path = require('path')
    //引入 html-webpack-pluign插件，作用将webpack打包后的js自动注入到html中
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    //引入mini-css-extract-plugin 插件，作用抽取css为单独文件
    module.exports = {
        entry:'./src/js/index.js',
        output:{
            filename: 'buit.js',
            path: Path.reslove(__dirname,'build')
        },
        module:{
            rules:[
                //样式loader
                {
                    test: /\.css$/,
                    use:[
                        MiniCssExtractPlugin.loader,
                        'css-loader'
                    ]
                },
                {
                    test:/\.less$/,
                    use:['style-loader','css-loader','less-loader']
                },
                //图片loader
                {
                    test:/\.(jpg|png|gif)$/,
                    loader: 'url-loader',
                    options:{
                        limit: 8*1024, 
                        esModule:false,
                        name: '[name].[hash:10].[ext]',
                        outPath:'img'
                    }
                },
                {
                    test: /\.html$/,
                    loader: 'html-loader'
                },
                {
                    //处理字体图标
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
                template: './src/index.html'
            }),
            new MiniCssExtractPlugin({
                //对输出文件进行重命名
                filename: 'css/built.css'
            })
        ],
        devServer:{
            contentBase: Path.reslove(__dirname,'build'),
            compress: true,
            port: 3000,
            open: true
        }
    }

```
