---
title: oneOf
date: 2020-05-22 14:29:17
tags:
---
webpack的 module是对一些文件的loader的配置，如：

```
const config = {
    module:{
        rules:[
            {
                test:/\.css$/,
                use:['style-loader','css-loader']
            },
            {
                test:/\.less$/,
                use:['style-loader','css-loader','less-loader']
            }
        ]
    }
}

module.exports = config

```

上述配置正常使用没有问题，但是从性能上考虑，webpack在对 .css|.less 文件进行loader编译时，每个css|less文件都要进行 css和less的loader匹配，当文件非常多的时候，就会造成性能很大的消耗

那么在webpack中就需要使用 oneOf 对这一块来进行优化。

很简单，直接在 rules 中添加oneOf就可以了
```
const config = {
    module:{
        rules:[
            oneOf:[
                 //js处理
                    {
                        test: /\.js$/, //js语法检查 eslint -->eslint-loader eslint-config-airbnb-base - eslint-plugin-import
                        exclude:/node_modules/,
                        enforce: 'pre',
                        loader:'eslint-loader', //package.json 配置 "eslintConfig": {"extends":"airbnb-base"}
                        options:{
                            fix:true //自动修复
                        }
                    },
                    {   //js兼容
                        test:/\.js$/, // babel: babel-loader-->@babel/core-->@babel/preset-env -- core-js -->@babel/poly-fill
                        exclude:/node_modules/,
                        loader:'babel-loader',
                        options:{
                            presets:[
                                [
                                    "@babel/preset-env",
                                    {
                                        useBuiltIns: 'usage', //按需加载
                                        corejs: 3, //指定corejs版本
                                        targets:{ //指定兼容到哪个版本
                                            "chrome":"60",
                                            "firefox":"60",
                                            "ie":"9",
                                            "safari":"10",
                                            "edge":"11"
                                        }
                                    }

                                ]
                            ]
                        } 
                    },
                    //css样式
                    {
                        test:/\.css$/,
                        use: [...commonCssConfig]
                    },
                    {
                        test:/\.less$/,
                        use:[ ...commonCssConfig,'less-loader']
                    },
                    //其他
                    ...
            ]
        ]
    }
}

module.exports = config

```

在 oneOf下的loader只会匹配一个。
* 注意不能有两个配置处理同一种类型文件
但 上述js有连个loader，怎么解决这个问题呢？

正常来讲，一个文件只能被一个loader处理,当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序,先执行 esLint 再执行babel.这里需要将eslint-loader拿到外层，优化生产环境打包速度

```
const config = {
    module:{
        rules:[

            {
                //js处理
                    {
                        test: /\.js$/, //js语法检查 eslint 
                        enforce: 'pre',
                        loader:'eslint-loader', //package.json 配置 "eslintConfig": {"extends":"airbnb-base"}
                        options:{
                            fix:true //自动修复
                        }
                    },
            },

            { 
              oneOf:[
                    {   //js兼容
                        test:/\.js$/, // babel: babel-loader-->@babel/core-->@babel/preset-env -- core-js -->@babel/poly-fill
                        exclude:/node_modules/,
                        loader:'babel-loader',
                        options:{
                            presets:[
                                [
                                    "@babel/preset-env",
                                    {
                                        useBuiltIns: 'usage', //按需加载
                                        corejs: 3, //指定corejs版本
                                        targets:{ //指定兼容到哪个版本
                                            "chrome":"60",
                                            "firefox":"60",
                                            "ie":"9",
                                            "safari":"10",
                                            "edge":"11"
                                        }
                                    }

                                ]
                            ]
                        } 
                    },
                    //css样式
                    {
                        test:/\.css$/,
                        use: [...commonCssConfig]
                    },
                    {
                        test:/\.less$/,
                        use:[ ...commonCssConfig,'less-loader']
                    },
                    //其他
                    ...
            }   ]
        ]
        
    }
}

module.exports = config
```