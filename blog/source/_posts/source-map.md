---
title: source-map
date: 2020-05-21 09:39:24
tags:
---
source-map ： 一种提供 源代码 到 构建后代码 映射的技术，如果构建后代码出错了，通过映射可以追踪源代码错误，便于问题定位，开发调试
* 开启source-map
```
const config = {
    devtool: 'source-map'
}

module.exports = config
```

打开控制台，运行webpack：
![运行结果](source-map.png)

打包后的文件生成了 .map 文件

修改 devtool的值为 inline-source-map

```
const config = {
    devtool: 'inline-source-map'
}

module.exports = config
```
打开控制台，运行webpack：
![运行结果](inline-source-map.jpg)

inline-source-map打包后没有生成.map文件。

点击查看打包后的 built.js文件,在bult.js文件内部生成了以 sourceMappingURL 为索引指向源代码的文件 

![inline-source-map-built](inline-source-map-built.jpg)

#### 内联和外部
生成 外部.map文件的为外部；在内部生成指向源代码文件，不生成.map文件为内联

```
source-map ：外部
inline-source-map ：内联

内联和外部的区别：

1.外部生成了 .map 文件，内联不生成 
2.内联构建速度更快

```

#### devtool配置值
```
    devtool: [inline-|hidden-|eval-][nosources-][cheap-[module-]] source-map

    souce-map: 外部
    错误代码准确信息 和 源代码的错误位置

    inline-source-map: 内联
    只生成一个内联source-map
    错误代码准确信息 和 源代码的错误位置

    hidden-source-map: 外部
    错误代码错误原因，但是没有源代码错误位置
    不能追踪源代码错误，只能提示到构建后代码的错误位置

    eval-sourve-map：内联
    错误代码准确信息 和 源代码的错误位置
    每一个文件都生成对应的source-map,都在eval

    nosources-source-map ：外部
    错误代码精确信息，但是没有任何代码信息

    cheap-source-map：外部
    错误代码准确信息 和 源代码的错误位置
    只能精确到行

    cheap-module-source-map: 外部
    错误代码精确信息 和 源代码的错误位置
    modul会将loader的source map 加入

    开发环境： 速度快，调试更友好
    速度快（eval>inline>cheap>...）
        eval-cheap-source-map
        eval-source-map
    调试更友好
        source-map
        cheap-module-source-map
        cheap-source-map
    
    --> eval-source-map / eval-cheap-module-source-map

    生产环境：源代码要不要隐藏？ 调试要不要更友好
    内联会让代码体积变大，所以在生成环境不用内联
    nosources-source-map  全部隐藏
    hidden-source-map 只隐藏源代码，只会提示构建后代码错误信息

    --> source-map / cheap-module-source-map

```
#### 综上：

#### 开发环境：
```
eval-source-map / eval-cheap-module-source-map

```
#### 生产环境：
```
source-map / cheap-module-sourcee-map
```

#### PS: create-react-app中
```

开发环境： cheap-module-source-map

生产环境：source-map

```