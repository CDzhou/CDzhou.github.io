---
layout:     post
title:      vue-cli 3中dart-sass替换node-sass
subtitle:   vue-cli 3中dart-sass替换node-sass
date:       2020-06-06
author:     CDZ
header-img: img/webpack.png
catalog: true
tags:
    - webpack
    - node-sass
    - vue-cli
    - vue
---
> 如果你使用过 sass ，应该了解多年来 node-sass 一直是 JavaScript 社区里的主流选择，它实际上只是 libsass 在 node 环境下的一个 wrapper， 编译 sass 文件的实际工作是 libsass 完成的。

> 在使用 node-sass 过程中遇到的很多问题实际上也是 libsass 引发的，libsass 是用 C/C++ 实现的，常见的问题是，在安装 node-sass 的过程中经常会出现安装失败的情况，又或者切换了 Node.js 版本发现 node-sass 需要重新安装才能用，如果你在 docker 中安装 node-sass 还会遇到由于缺少各种依赖导致 node-sass build 失败的情况，又或者在国内由于网络原因导致 node-sass 需要的二进制文件下载不下来而 build 失败。

现在，sass 官方已经使用 dart-sass 作为 sass 的主要实现：


> Dart Sass is the primary implementation of Sass, which means it gets new features before any other implementation. It's fast, easy to install, and it compiles to pure JavaScript which makes it easy to integrate into modern web development workflows.

替换过程：
1. 首先把package.json中的node-sass删除掉，执行：
```language
npm i sass sass-loader -D
```
2. 在vue.config.js中修改sass-loader的配置：

```language
module.exports = {
  css: {
    loaderOptions: {
      sass: {
        implementation: require('sass'), // This line must in sass option
      },
    },
  }
//other code

};
```

完成！

