---
title: js兼容
date: 2020-05-18 10:26:17
tags:
---
### js语法检查回顾：
安装的插件：
```
eslint --> eslint-loader --> eslint-config-airbnb-base --> eslint-plugin-import

```
webpack.config.js

```
const config = {
        module:{
            rules:[
                {
                    test:/\.js$/,
                    exclude:/node_modules/,
                    loader:"eslint-loader",
                    options:{
                        fix:true //自动修复
                    }
                }
            ]
        }
}
module.exports = config

```

package.json配置
```
{
    "eslintConfig":{
        "extends":"airbnb-base"
    }
}
```

### js语法兼容处理
js 兼容性处理需要用到babel插件，安装以下插件：
```
babel-loader --> @babel/core --> @babel/preset-env

```
webpack.config.js
```
//第一种配置：直接配置
// 这种写法有个问题：只能转换基本语法，如promise高级语法不能转换
const config = {
    module:{
        rules:[
            {
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'babel-loader',
                options:{
                    //预设： 指示 babel做怎样的兼容性处理
                    presets:[
                        "@babel/preset-env"
                    ]
                }
            }
        ]
    }
}

module.exports = config
```

第二种配置： 全部js兼容性处理 --> @babel/polyfill  

安装：
```
cnpm i @babel/polyfill -D

```

使用：
入口文件index.js直接引入：
```
import ' @babel/polyfill'

```
第二种这种配置会将将所有兼容性代码全部引入，造成最后打包的体积太大，而实际上我们只需要解决部分兼容性问题。

有没有可以按需加载兼容处理的呢? 这里就要介绍三种方法了

第三种方法： 按需加载兼容，通过 core-js 来实现

安装 core-js
```
cnpm i core-js -D
```
webpack.config.js中配置
```
const config = {
    module:{
        rules:[
            {
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'babel-loader',
                options:{
                    //预设： 指示 babel做怎样的兼容性处理
                    presets:[
                        
                        [
                            "@babel/preset-env",
                            {
                                //按需加载
                                useBuiltIns: 'useage',
                                //指定core-js版本
                                corejs:{
                                    version: 3
                                },
                                targets:{ //指定兼容到哪个版本
                                    chrome: '60',
                                    firefox: '60',
                                    ie:'9',
                                    safari: '10',
                                    edge:'11'
                                }
                            }

                        ]
                    ]
                }
            }
        ]
    }
}

module.exports = config
```

### wabpck.config.js 整体配置
```

const Path = require('path')

const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')

process.env.NODE_ENV = 'development'

const Config = {
    entry: './src/js/index.js',
    output:{
        filename: 'js/built.js',
        path: Path.resolve(__dirname,'build')
    },
    module:{
        rules:[
            // {
            //     test:/\.js$/,
            //     exclude:/node_modules/,
            //     loader: 'eslint-loader',
            //     options:{
            //         fix:true
            //     }
            // },
            {
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'babel-loader',
                options:{
                    presets:[
                        [
                            '@babel/preset-env',
                            {
                                //按需加载
                                useBuiltIns: 'usage',
                                //指定core-js版本
                                corejs:{
                                    version: 3
                                },
                                //指定兼容性做到哪个版本
                                targets:{
                                    chrome: '60',
                                    firefox: '60',
                                    ie:'9',
                                    safari: '10',
                                    edge:'11'
                                }
                            }
                        ]

                    ]
                }
            },
            {
                test: /.css$/,
                use:[
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    {
                        loader:'postcss-loader',
                        options:{
                            ident:'postcss',
                            plugins:()=>[
                                require('postcss-preset-env')()
                            ]
                        }
                    }
                ]
            },
            {
                test:/\.less$/,
                use:['style-loader','css-loader','less-loader']
            },
            {
                test:/\.(png|jpg|gif)$/,
                loader:'url-loader',
                options:{
                    limit: 8*1024,
                    name:'[name].[hash:10].[ext]',
                    esModule:false,
                    outputPath:'imgs'
                }
            },
            {
                test:/\.html$/,
                loader:'html-loader'
            },
            {
                test:/\.(ttf|woff|woff2|eot|svg)$/,
                loader:'file-loader',
                options:{
                    name:'[hash:10].[ext]',
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
            filename:'css/built.css'
        }),
        new OptimizeCssAssetsWebpackPlugin()
    ],
    devServer:{
        contentBase: Path.resolve(__dirname,'build'),
        compress:true,
        port:3000,
        open:true
    }
}
module.exports = Config

```

### babel 单独配置，使用 .babelrc

根目录下新建 .babelrc 文件,当使用babel-loader时，如果没有配置options，babel会自己去找 .babelrc然后加载其中的配置

.babelrc 简单配置
```
{
    "presets":[
        "@babel/preset-env",[
            {
                "useBuiltIns":"usage", //按需加载
                "corejs": 3, //core-js版本
                "targets":{ //指定浏览器版本
                    "chrome": "60",
                    "firefox": "60",
                    "ie": "9",
                    "safari": "10",
                    "edge":"11"

                }
            }
        ]
    ]
}
```
webpack.config.js

```
const config = {
    module:{
        rules:[
            {
                test:/\.js$/,
                loader: 'babel-loader'
            }
        ]
    }
}

module.exports = config

```