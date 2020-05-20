---
title: 开发环境优化-HMR
date: 2020-05-20 15:38:46
tags:
---

前面配置中，无论是js还是css只要有一个文件改动，都会造成所有模块全部重新打包。项目中引入的模块较少时，影响不大。而当项目中引入上千个模块时，每次改动都会造成这些模块的重新打包，打包构建效率大大降低，影响开发效率。

那么能不能在修改后，只重新打包修改的模块呢？ HMR就是为解决这个问题而生。

HMR ： hot module replacement 热模块替换/模块热替换
作用：当一个模块发生变化，只会重新打包这一个模块，而不是打包所有模块，极大提升构建速度

#### 开启热模块更新功能：

webpack.config.js中devServer开启 热更新功能

```
devServer:{
    contentBase:'build',
    compress: true,
    port:3000,
    open:true,
    hot: true //开启HMR功能
}

```
目录结构：
```
    HMR
     |- node_modules
     |- src
     |   |- css
     |   |   |-iconfont.css
     |   |   |-index.less
     |   |- image
     |   |   |-angular.png
     |   |   |-vue.png
     |   |- js
     |   |   |-index.js
     |   |   |-print.js
     |   |- media
     |   |   |-iconfont.eot
     |   |   |-iconfont.svg
     |   |   |-iconfont.ttf
     |   |   |-iconfont.woff
     |   |- index.html
     |-webpack.config.js
     |-package.json

```

index.js 文件
```
/**
 * 打包入口文件
 */

 //引入css
 import '../css/iconfont.css'
 //引入less
 import '../css/index.less'

 import print from './print'

 console.log('index文件加载了')

 print()

const add = (x,y)=> x+y

console.log(add(1,5))


```
print.js 文件

```
console.log('printjs加载了')

const print = ()=>{
  const content = 'print打印了'
  console.log(content)
}
export default print

```

#### 启动项目 
浏览器控制台输出：
```
[HMR] Waiting for update signal from WDS...
print.js:2 printjs加载了
index.js:18 index文件加载了
print.js:6 print打印了
index.js:24 6
DevTools failed to load SourceMap: Could not load content for webpack:///node_modules/_sockjs-client@1.4.0@sockjs-client/dist/sockjs.js.map: HTTP error: status code 404, net::ERR_UNKNOWN_URL_SCHEME
client:48 [WDS] Hot Module Replacement enabled.
client:52 [WDS] Live Reloading enabled.
```
从浏览器控制台输出可以看出，各个模块加载ok

### 样式文件HMR功能
样式配置：
```
    const config = {
        module:{
            rules:[
                {
                    //处理less资源
                    test:/\.less$/,
                    use:['style-loader','css-loader','less-loader']
                },
                {
                    //处理css资源
                    test: /\.css$/,
                    use:['style-loader','css-loader']
                },
            ]
        }
    }
    module.exports = config

```
下面我们来修改 index.less文件，将图片的宽度由原来的200px,改为300px。
再来看看浏览器控制台输出：
```
[HMR] Waiting for update signal from WDS...
print.js:2 printjs加载了
index.js:18 index文件加载了
print.js:6 print打印了
index.js:24 6
DevTools failed to load SourceMap: Could not load content for webpack:///node_modules/_sockjs-client@1.4.0@sockjs-client/dist/sockjs.js.map: HTTP error: status code 404, net::ERR_UNKNOWN_URL_SCHEME
client:48 [WDS] Hot Module Replacement enabled.
client:52 [WDS] Live Reloading enabled.

2client:55 [WDS] App updated. Recompiling...
reloadApp.js:19 [WDS] App hot update...
log.js:24 [HMR] Checking for updates on the server...
log.js:24 [HMR] Updated modules:
log.js:16 [HMR]  - ./src/css/index.less //只有less模块重新编译
log.js:24 [HMR] App is up to date.
```
从上面操作可以看出，在没有对样式文件进行处理的情况下，样式文件可以使用HMR功能，这是因为 style-loader内部实现了HMR功能
因此在开发模式中通常使用 style-loader 这样不必单独对样式文件进行HMR处理。

### js文件HMR

修改一下入口文件print.js中代码：
```
console.log('printjs加载了')

const print = ()=>{
  const content = 'print打印了0000'
  console.log(content)
}
export default print
```
来看下浏览器控制台输出：
```
[HMR] Waiting for update signal from WDS...
print.js:2 printjs加载了
index.js:18 index文件加载了
print.js:6 print打印了0000
index.js:24 6
```
发现整个包重新构建了，说明默认不能使用HMR功能。那么如果JS要使用HMR,那么需要修改js代码，添加支持 HMR功能的代码 
* 注意： HMR功能对js的处理，只能处理非入口js文件的其他文件

在 index.js文件中新增以下代码:
```
if(module.hot){
  //一旦module.hot 为true,说明开启了HMR功能 --> 让 HMR功能代码生效
  module.hot.accept('./print.js',function(){
    //方法会监听 print.js文件的变化，一旦发生变化，其他默认不会重新打包构建。
    //会执行后面的回调函数
    print()
  })
}
```
保存，修改 print.js 中的代码：
```
console.log('printjs加载了')

const print = ()=>{
  const content = 'print打印了'
  console.log(content)
}
export default print
```

控制台输出：
```
[HMR] Waiting for update signal from WDS...
VM2890 print.js:2 printjs加载了
index.js:18 index文件加载了
VM2890 print.js:6 print打印了0000
index.js:24 6
DevTools failed to load SourceMap: Could not load content for webpack:///node_modules/_sockjs-client@1.4.0@sockjs-client/dist/sockjs.js.map: HTTP error: status code 404, net::ERR_UNKNOWN_URL_SCHEME
client:48 [WDS] Hot Module Replacement enabled.
client:52 [WDS] Live Reloading enabled.

2client:55 [WDS] App updated. Recompiling...
reloadApp.js:19 [WDS] App hot update...
log.js:24 [HMR] Checking for updates on the server...
print.js:2 printjs加载了
print.js:6 print打印了      //print.js实现了HMR功能
log.js:24 [HMR] Updated modules:
log.js:24 [HMR]  - ./src/js/print.js
log.js:24 [HMR] App is up to date.
```

### html文件 HMR
修改 index.html文件
```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>webapck</title>
</head>

<body>
  <H1>开发环境配置000</H1> //修改处: 开发环境配置 --> 开发环境配置000
  <div id="box2"></div>
  <div id="box3"></div>
  <img src="./image/vue.png" alt="">
  <span class="iconfont icon-icon-test"></span>
  <span class="iconfont icon-icon-test1"></span>
  <span class="iconfont icon-icon-test2"></span>
  <span class="iconfont icon-icon-test3"></span>

</body>

</html>
```
控制台并无变化。html文件：默认不能使用HMR功能，html文件不能热更新了
 *  解决：修改entry入口，将html文件引入

 ```
 const config = {
     entry: ['./src/js/index.js','./src/index.html'],
     output:{
        filename:'js/built.js',
        path:Path.resolve(__dirname,'build')
    }
 }

 module.exports = config

 ```
 再次修改 index.html中的内容，发现控制台发生了变化,包重新构建一次
 
 * 因热更新是多个模块下，只更新发生改变的模块，提高构建速度。而index.html只有一个文件，因此不需要HMR功能 