---
title: js语法检查
date: 2020-05-15 10:09:20
tags:
---

在项目中，团队代码规范，风格保持一致，对于项目维护、开发效率来说很重要。因此项目中需要进行必要的语法检查，保持代码风格统一，提高开发效率。目前使用比较广泛的就是 eslint了。

安装插件： eslint eslint-loader
```
cnpm i eslint eslint-loader -D
```
设置检查规则：在github中排名最高的为 airbnb-base，推荐使用

使用airbnb-base, 需要安装eslint-config-airbnb-base eslint eslint-plugin-import

```
//安装插件(eslint上一步已安装，这里不再安装)
cnpm i eslint-config-airbnb-base eslint-plugin-import -D

```
webpack.config.js配置
```
    const config = {
        module:{
            rules:[
                {
                    test:/\.js$/,
                    loader:'eslint-loader'
                }
            ]
        }
    }
```
package.json中配置

```
    {
        "eslintConfig":{
            ""extends":"airbnb-base"
        }
    }

```
项目入口文件index.js
```
const add = (x,y)=>x+y
console.log(add(10,20))

```

目前配置已完成，把项目在终端中打开，运行：webpack，发现如下报错
```
ERROR in ./src/js/index.js
Module Error (from ../node_modules/_eslint-loader@4.0.2@eslint-loader/dist/cjs.js):

C:\Users\Administrator\Desktop\wproject\15.js语法检测\src\js\index.js
  1:26  error    Expected linebreaks to be 'LF' but found 'CRLF'  linebreak-style
  1:26  error    Missing semicolon                                semi
  2:27  error    Expected linebreaks to be 'LF' but found 'CRLF'  linebreak-style
  2:27  error    Missing semicolon                                semi
  3:1   error    Expected linebreaks to be 'LF' but found 'CRLF'  linebreak-style
  4:1   error    Expected linebreaks to be 'LF' but found 'CRLF'  linebreak-style
  5:15  error    A space is required after ','                    comma-spacing
  5:17  error    Missing space before =>                          arrow-spacing
  5:22  error    Operator '+' must be spaced                      space-infix-ops
  5:24  error    Expected linebreaks to be 'LF' but found 'CRLF'  linebreak-style
  5:24  error    Missing semicolon                                semi
  6:1   warning  Unexpected console statement                     no-console
  6:19  error    A space is required after ','                    comma-spacing
  6:24  error    Newline required at end of file but not found    eol-last
  6:24  error    Missing semicolon                                semi

✖ 15 problems (14 errors, 1 warning)
  14 errors and 0 warnings potentially fixable with the `--fix` option.
```
从上述错误日志中，可看出是因为index.js中的代码，和airbnb-base中的检查规则不一致导致报错。那要一个一个手动改正吗？显然不可取。其实只需要一步即可修正一些常见的简单的规则。

webpack.config.js
```
const config = {
        module:{
            rules:[
                {
                    test:/\.js$/,
                    loader:'eslint-loader',
                    //在options中配置 fix:true 即可自动修复基本语法规则
                    options:{
                        fix:true
                    }
                    
                    
                }
            ]
        }
    }
```
再次在终端中打开项目，运行 webpack
```
C:\Users\Administrator\Desktop\wproject\15.js语法检测>webpack
Hash: 777adccf3aa8d036c15a
Version: webpack 4.43.0
Time: 2408ms
Built at: 2020-05-15 10:05:22
        Asset       Size  Chunks             Chunk Names
     built.js   17.9 KiB    main  [emitted]  main
css/built.css   18 bytes    main  [emitted]  main
   index.html  386 bytes          [emitted]
Entrypoint main = css/built.css built.js
[../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_less-loader@5.0.0@less-loader/dist/cjs.js!./src/css/index.less] 288 bytes {main}
[built]
[./src/css/index.css] 39 bytes {main} [built]
[./src/css/index.less] 635 bytes {main} [built]
[./src/js/index.js] 107 bytes {main} [built] [1 warning]
    + 3 hidden modules

WARNING in ./src/js/index.js
Module Warning (from ../node_modules/_eslint-loader@4.0.2@eslint-loader/dist/cjs.js):

C:\Users\Administrator\Desktop\wproject\15.js语法检测\src\js\index.js
  6:1  warning  Unexpected console statement  no-console

✖ 1 problem (0 errors, 1 warning)

Child HtmlWebpackCompiler:
     1 asset
    Entrypoint HtmlWebpackPlugin_0 = __child-HtmlWebpackPlugin_0
    [../node_modules/_html-webpack-plugin@4.3.0@html-webpack-plugin/lib/loader.js!./src/index.html] 406 bytes {HtmlWebpackPlugin_0} [built]
Child mini-css-extract-plugin ../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_postcss-loader@3.0.0@postcss-loader/src/index.js??postcss!src/css/index.css:
    Entrypoint mini-css-extract-plugin = *
    [../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_postcss-loader@3.0.0@postcss-loader/src/index.js?!./src/css/index.css] ../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_postcss-loader@3.0.0@postcss-loader/src??postcss!./src/css/index.css 288 bytes {mini-css-extract-plugin} [built]
        + 1 hidden module


```
项目打包成功，但是有一个waring(稍后再看),再来看项目入口文件 index.js中的代码。
修复前：
```
const add = (x,y)=>x+y
console.log(add(10,20))

```
修复后：
```
const add = (x, y) => x + y; //eslint自动修复了代码
console.log(add(10, 20));
```
eslint自动修复Ok,现在我们来看看刚才出现的waring
```
C:\Users\Administrator\Desktop\wproject\15.js语法检测\src\js\index.js
  6:1  warning  Unexpected console statement  no-console

✖ 1 problem (0 errors, 1 warning)
```

no-console 说明是index.js中的 console.log(add(10,20)) ,airbnb不建议在项目中使用console,解决如下
index.js
```
const add = (x, y) => x + y; //eslint自动修复了代码
//eslint-diable-next-line
console.log(add(10, 20));  //这一行代码不进行eslint检测

```
再次在终端中运行：webapck

```
C:\Users\Administrator\Desktop\wproject\15.js语法检测>webpack
Hash: 2caa20ab5beed81950bc
Version: webpack 4.43.0
Time: 2387ms
Built at: 2020-05-15 10:06:04
        Asset       Size  Chunks             Chunk Names
     built.js   17.9 KiB    main  [emitted]  main
css/built.css   18 bytes    main  [emitted]  main
   index.html  386 bytes          [emitted]
Entrypoint main = css/built.css built.js
[../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_less-loader@5.0.0@less-loader/dist/cjs.js!./src/css/index.less] 288 bytes {main}
[built]
[./src/css/index.css] 39 bytes {main} [built]
[./src/css/index.less] 635 bytes {main} [built]
[./src/js/index.js] 140 bytes {main} [built]
    + 3 hidden modules
Child HtmlWebpackCompiler:
     1 asset
    Entrypoint HtmlWebpackPlugin_0 = __child-HtmlWebpackPlugin_0
    [../node_modules/_html-webpack-plugin@4.3.0@html-webpack-plugin/lib/loader.js!./src/index.html] 406 bytes {HtmlWebpackPlugin_0} [built]
Child mini-css-extract-plugin ../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_postcss-loader@3.0.0@postcss-loader/src/index.js??postcss!src/css/index.css:
    Entrypoint mini-css-extract-plugin = *
    [../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_postcss-loader@3.0.0@postcss-loader/src/index.js?!./src/css/index.css] ../node_modules/_css-loader@3.5.3@css-loader/dist/cjs.js!../node_modules/_postcss-loader@3.0.0@postcss-loader/src??postcss!./src/css/index.css 288 bytes {mini-css-extract-plugin} [built]
        + 1 hidden module

```
waring已经没有了，但是在实际项目中最好不要使用 //eslint-diable-next-line

### 完整配置
package.json
```
{
  "name": "wepack_test",
  "version": "1.0.0",
  "description": "",
  "main": "webpack.config.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "dev": "webpack-dev-server --open"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "css-loader": "^3.5.2",
    "eslint": "^7.0.0",
    "eslint-config-airbnb-base": "^14.1.0",
    "eslint-loader": "^4.0.2",
    "eslint-plugin-import": "^2.20.2",
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
  "browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      ">0.2%",
      "not dead",
      "not op_min all"
    ]
  },
  "eslintConfig":{
    "extends":"airbnb-base"
  }
}

```

webpack.config.js
```
const Path = require('path')

const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')

//css兼容需设置nodejs 中的环境变量
process.env.NODE_ENV = 'prodction'

module.exports = {
    entry: './src/js/index.js',
    output:{
        filename: 'built.js',
        path: Path.resolve(__dirname,'build')
    },
    module:{
        rules:[
            {   
                /**
                 * 只检查自己写的源代码，第三方的库不用检查
                 * 
                 * eslint eslint-loader 
                 * 
                 * 插件airbnb： eslint-config-airbnb-base eslint-plugin-import
                 * 
                 * package.json中配置
                 * 
                 * "eslintConfig":{
                 *      "extends":"airbnb-base"
                 *  }
                 * 
                 */
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'eslint-loader',
                options:{
                    fix: true
                }
            },
            {   
                test:/\.css$/,
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
                use:[
                    'style-loader',
                    'css-loader',
                    'less-loader'
                ]
            },
            {
                test:/\.(jpg|png|gif)$/,
                loader:'url-loader',
                options:{
                    limit: 8*1024,
                    name:'[hash:10].[ext]',
                    esModule:false,
                    outputPath:'imgs'
                }
            },
            {
                test:/\.html$/,
                loader:'html-loader'
            },
            {
                test:/\.(woff|woff2|ttf|eot|svg)$/,
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
            template:'./src/index.html'
        }),
        new MiniCssExtractPlugin({
            filename:'css/built.css'
        }),
        new OptimizeCssAssetsWebpackPlugin()
    ],
    devServer:{
        contentBase:Path.resolve(__dirname,'build'),
        compress:true,
        port:3000,
        open:true
    }
}
```