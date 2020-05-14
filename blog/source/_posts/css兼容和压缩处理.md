---
title: css兼容处理和压缩
date: 2020-05-14 13:23:24
tags:
---
### css兼容

前面css-loader将css处理为js资源是的webpack能够引入样式资源，style-loader将处理后的样式资源插入到head中。但是页面加时，css先转为js再引入到页面时，会造成页面闪白。所以通过 mini-css-extract-plugin 将css资源通过link形式引入，解决页面闪白的问题。
实际开发中还要考虑css对各浏览器的兼容性处理，为了解放开发人员，让开发人员专注于开发工作，这种兼容性工作通常使用插件处理。
```
postcss: 处理兼容性的插件

使用postcss需要下载:   postcss-loader --> postcss-preset-env

postcss-preset-env 插件的作用：
帮助 postcss 找到 package.json 中的 browerslist 里面的配置，通过配置加载指定的css兼容性样式

```
使用postcss：

```
//安装postcss-loader、postcss-preset-env
cnpm i postcss-loader postcss-preset-env -D

```
webpack.config.js

```
const MiniCssExtractPlugin = require('mini-css-extract-pluign')
const config = {
    module:{
        rules:[
            {
                test: /\.css$/,
                use:[
                   MiniCssExtractPlugin.loader,
                   'css-loader',
                    {
                        loader: 'postcss-loader',
                        options:{
                            indent: 'postcss', //固定写法
                            pulgins:()=>[
                                //插件帮助 postcss找到package.json中 'browserslist'中的配置，加载指定的css样式
                                require('postcss-preset-env')()
                            ]
                        }
                    }
                ]
            }
        ]
    }
}

module.exports = config

```

package.json
```
{
  "browserslist":{   //postcss 配置
    "development":[
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production":[
      ">0.2%",
      "not dead",
      "not op_min all"
    ]
  }
}

```

### css压缩

实际打包中，为减少打的包体积，代码压缩是常用的手段。css压缩也不例外。
通常使用 optimize-css-assets-webpack-plugin 来进行css压缩

安装optimize-css-assets-webpack-plugin插件
```
cnpm i optimize-css-assets-webpack-plugin -D

```
使用插件
webpack.config.js
```
//引入optimize-css-assets-webpack-plugin
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const config = {
  plugins:[
    new OptimizeCssAssetsWebpackPlugin() //基础使用
  ]
}

```

### 完整配置

webpack.config.js
```
  //引入路径
  const Path = require('path')

  //引入 html-webpack-plugin 打包后的js自动注册入html中
  const HtmlWebpackPlugin = require('html-webpack-plugin')

  //引入mini-css-extract-plugin，css link引入
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')

  //引入 optimize-css-assets-webpack-plugin, 压缩css
  const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')

  module.exports = {
    entry: './src/index.js',
    output:{
      filename: 'built.js',
      path: Path.reslove(__dirname,'build')
    },
    module:{
      rules:[
        //样式资源
        {
          test:/\.css$/,
          use:[
            MiniCssExtractPlugin.loader,
            'css-loader',
            {
              loader: 'postcss-loader',
              options:{
                ident: 'postcss',
                plugins:()=>[
                  require('postcss-preset-env')()  //帮助postcss找到package.json中的'browserslist'配置，加载指定的兼容样式
                ]
              }
            },
            {
              test:/.less$/,
              use:['style-loader','css-loader','less-loader']
            },
            //图片资源
            {
              test:/\.(jpg|png|gif)$/,
              loader:'url-loader',
              options:{
                limit: 8*1024,
                esModule:false,
                name:'[name].[hashL:10].[ext]',
                outputPath:'imgs'
              }
            },
            {
              test:/\.html$/,
              loader:'html-loader'
            },
            //字体图标资源
            {
              test:/\.(woff2|eot|ttf|woff|svg)$/,
              loader: 'file-loader',
              options:{
                name:'[hash:10].[ext]',
                outputPath:'media'
              }
            }
          ]
        }
      ]
    },
    mode: 'development',
    plugins:[
      new HtmlWebpackPlugin({
        template:'./src/index.html'
      }),
      new MiniCssExtractPlugin({
        //对输出的文件进行重命名
        filename: 'css/built.css'
      }),
      new OptimizeCssAssetsWebpackPlugin() //压缩css
    ],
    devServer:{  //webapck-dev-server
      contentBase: Path.reslove(__dirname,'build'),
      port:3000,
      compress:true,
      open:true
    }
  }

```

package.json

```
{
  "name": "wepack_test",
  "version": "1.0.0",
  "description": "",
  "main": "webpack.config.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "css-loader": "^3.5.2",
    "file-loader": "^6.0.0",
    "html-loader": "^1.1.0",
    "html-webpack-plugin": "^4.2.0",
    "less": "^3.11.1",
    "less-loader": "^5.0.0",
    "mini-css-extract-plugin": "^0.9.0",
    "optimize-css-assets-webpack-plugin": "^5.0.3",
    "postcss-loader": "^3.0.0",
    "postcss-preset-env": "^6.7.0",
    "style-loader": "^1.1.4",
    "url-loader": "^4.1.0",
    "webpack": "^4.42.1",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  },
  "browserslist":{
    "development":[
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production":[
      ">0.2%",
      "not dead",
      "not op_min all"
    ]
  }
}


```