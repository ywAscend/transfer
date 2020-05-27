---
title: tree-shaking
date: 2020-05-27 13:33:31
tags:
---
tree shaking 所谓的“摇树”，是指删除无用的代码，这样在构建大型应用时，可以大大减少打包的体积

### 使用条件
```
 1. 必须使用es6模块 
 2. 开启production环境
 3. 在package.json中配置

```
#### 1.必须使用es6模块
必须遵循 ES6 的模块规范 (import & export)，如果是 CommonJS 规范 (require) 则无法使用。只打包某一模块中被使用的代码。

* 模块a.js
```
export const add (x,y) => x+y

const test = () => {
    consle.log('print test')
}

```
* 模块b.js
```
import {add} from './a.js'
add()

```
* 入口文件：index.js
```
import './b.js'

...

```

最终webpack在打包时，只会打包 a 模块中被用到的 add 相关的代码块

#### 2.开启production
生产环境下会自动开启

#### 3. package.json中配置 sildeEffects

sideEffects是webpack4新增的一个特性，通过给package.json加入sideEffects:false声明该模块是否含有副作用，为tree-shaking 提供更大的优化空间。

什么是副作用？

```
//test.js
export function test (t) { return t }
console.log(test(6))

```
tree-shaking后test模块变为：
```
console.log( function (t)  { return v }(6) )

```
这样 test 模块导出被忽略，但是副作用代码被保留，很多即使经过 tree shaking ，然而 bundle size 还是没有明显的减小。而通常我们期望的是 test 模块既然不被使用了，其中所有的代码应该不被引入才对。
这时就需要用到 sideEffects了。如果被引入的 包/模块 被标记为 sideEffects: false 了，那么不管它是否真的有副作用，只要它没有被引用到，整个 模块/包 都会被完整的移除。

在package.json中配置
```
{
    "sideEffects":false
}
```
当我们配置了 "sideEffects": false ，代表所有代码都没有副作用（都可以进行tree shaking）。
但是这还有个问题：可能会把css / @babel/polyfill （副作用）文件干掉

那么我们只需要为指定的文件配置sideEffects即可，修改sileEffects中的配置为
```
{
    "sideEffects":["*.css"] //.css文件具有副作用，tree-shaking时不被错误删除
}
```

### 开发环境使用 tree-shaking

在webpack配置中加入以下配置即可：
```
const config = {
    optimization:{
        usedExports: true
    }
}
module.exports = config

```