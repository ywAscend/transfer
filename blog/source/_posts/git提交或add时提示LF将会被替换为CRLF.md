---
title: git提交或add时提示LF将会被替换为CRLF
date: 2020-05-14 17:14:31
tags:
---
什么是LF和CRLF ?
1.LF和CRLF都是换行符，在各操作系统下，换行符是不一样的，Linux/UNIX下是LF,而Windows下是CRLF，早期的MAC OS是CR,后来的OS X在更换内核后和UNIX一样也是LF.
这种不统一确实对跨平台的文件交换带来了麻烦。虽然靠谱的文本编辑器和 IDE 都支持这几种换行符，但文件在保存时总要有一个固定的标准啊，比如跨平台协作的项目源码，到底保存为哪种风格的换行符呢？

2.Git 由大名鼎鼎的 Linus 开发，最初只可运行于 *nix 系统，因此推荐只将 UNIX 风格的换行符保存入库。但它也考虑到了跨平台协作的场景，并且提供了一个“换行符自动转换”功能。

安装好 GitHub 的 Windows 客户端之后，这个功能默认处于“自动模式”。
当你在签出文件时，Git 试图将 UNIX 换行符（LF）替换为 Windows 的换行符（CRLF）；当你在提交文件时，它又试图将 CRLF 替换为 LF。
所以Git在拉取代码的时候，git会自动将代码之中与你当前系统不同的换行方式自动转换成当前系统的换行方式。
这样一来在提交代码的时候，git会认为你未修改内容的文件也认为是修改过的，然后提示你warning: LF will be replaced by CRLF这样的信息。
3.上面的解决办法固然解决了很多人的问题，但是在某些情况下就不适用。这次项目中，在用fiskit框架npm deploy打包前端文件到后端路径的时候，打包出来的文件用git进行commit的时候，提示LF和CRLF，文件的换行符被修改过，这时候也尝试了上面的方法，也没有用，最后在知乎上看到一篇文章https://www.zhihu.com/question/50862500，

有效的解决方法
如果设置core.autocrlf = false，那么很可能会出现CRLF和LF混合的情况，这样会导致一些问题，例如git diff 失去功能，会发现很多行代码并没有修改，然而被认为是修改过了。
首先core.autocrlf = true在windows上才是正确的选择，那么如何避免warning呢？还要有以下几个步骤：

添加.gitattributes
设置core.safecrlf = true
使用dos2unix、notepad++等工具来将LF转换成CRLF

使用 dos2unix 将CRLF转换为LF:

dos2unix命令用来将DOS格式的文本文件转换成UNIX格式的（DOS/MAC to UNIX text file format converter）
将UNIX格式文本文件转成成DOS格式的是unix2dos命令。

如果一次转换多个文件，把这些文件名直接跟在dos2unix之后。（注：也可以加上-o参数，也可以不加，效果一样）
```

dos2unix file1 file2 file3 
dos2unix -o file1 file2 file3 

```

如果批量替换public/components 目录下的所有文件使用如下命令：
```
find public/components/ -name "*" | xargs dos2unix
```
要更改文件格式的后缀为.js ,那么借助于下面的命令就可以轻松的实现批量替换格式：
批量替换为linux文件格式：
```
find public/components/ -name "*.js" | xargs dos2unix
```

批量替换为dos文件格式：
```
find public/components/ -name "*.js" | xargs unix2dos
```

