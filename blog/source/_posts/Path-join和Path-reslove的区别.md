---
title: Path.join和Path.reslove的区别
date: 2020-05-28 13:56:47
tags:
---
path是node中的模块，用来处理文件与目录的路径
#### 1. 引入path
```
const Path = require('path')
```
### 2. Path.join
Path.join() 方法使用平台特定的分隔符作为定界符将所有给定的 path 片段连接在一起，然后规范化生成的路径。

零长度的 path 片段会被忽略。 如果连接的路径字符串是零长度的字符串，则返回 '.'，表示当前工作目录。

Path.join()方法是将多个参数字符串合并成一个路径字符串

　　console.log(Path.join(__dirname,'a','b'));   假如当前文件的路径是E:/node/1,那么拼接出来就是E:/node/1/a/b。

　　console.log(Path.join(__dirname,'/a','/b','..'));  路径开头的/不会影响拼接，..代表上一级文件，拼接出来的结果是：E:/node/1/a
　　console.log(Path.join(__dirname,'a',{},'b'));   而且path.join()还会帮我们做路径字符串的校验，当字符串不合法时，会抛出错误：Path must be a string.

### 3. Path.reslove

Path.resolve()方法是以程序为根目录，作为起点，根据参数解析出一个绝对路径
　　以应用程序为根目录
　　普通字符串代表子目录
　　/代表绝对路径根目录
　　
　　console.log(Path.resolve());   得到应用程序启动文件的目录（得到当前执行文件绝对路径）   E:\zf\webpack\1\src
　　console.log(Path.resolve('a','/c'));   E:/c  ,因为/斜杠代表根目录，所以得到的就是E:/c
　　所以我们一般拼接的时候需要小心点使用/斜杠
　　console.log(Path.resolve(__dirname,'img/so'));  E:\zf\webpack\1\src\img\so   这个就是将文件路径拼接，并不管这个路径是否真实存在。
　　console.log(Path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif'))    E:\zf\webpack\1\src\wwwroot\static_files\gif\image.gif
　　这个是用当前应用程序启动文件绝对路径与后面的所有字符串拼接，因为最开始的字符串不是以/开头的。
　　..也是代表上一级目录。
