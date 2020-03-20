---
layout:     post
title:      如何开发一个webpack plugin
subtitle:   用于上传服务器的小插件
date:       2020-03-20
author:     CDZ
header-img: img/webpack.png
catalog: true
tags:
    - webpack
    - webpack plugin
    - 工作流程优化
---
# 前言
> *plugin是 webpack 的 支柱 功能。webpack 自身也是构建于，你在 webpack 配置中用到的相同的plugin系统之上！plugin目的在于解决 loader 无法实现的其他事。*

个人认为webpack最令人惊喜功能之一的是其plugin系统，webpack在构建过程中，提供了一系列的hooks给开发者，开发者可以注册各类各样的函数在hooks上以达到目的。这里是webpack plugin hooks的文档：
[compiler-hooks](https://webpack.docschina.org/api/compiler-hooks/)
[compilation-hooks](https://webpack.docschina.org/api/compilation-hooks/)
本篇文章用自己做的一个小工具来展示如何编写一个plugin。

# 示例
## 源代码
```
const client = require("./scp.js");
const fs = require("fs");
const { resolve } = require("path");
const zlib = require("zlib");
const compressing = require("compressing");
class cdScp {
  constructor(option) {
    this.path = option.path;
    this.sshOption = option.ssh;
  }
  apply(compiler) {
    compiler.plugin("afterEmit", async (compilation, callback) => {
      let output = resolve(this.path, "../dist.zip");
      try {
        let stat = fs.lstatSync(this.path);
        if (!stat.isFile()) {
          //如果是文件夹
          await compressing.zip.compressDir(this.path, output);
          client.scp(output, this.sshOption, function(err) {
            console.log(err || "上传成功");
          });
        } else {
          client.scp(this.path, this.sshOption, function(err) {
            console.log(err || "上传成功");
          });
        }
      } catch (err) {
        console.error(err);
      }
    });
  }
}

module.exports = cdScp;
```

## 使用方法
需要在webpack的plugins属性配置
### 通过账号密码连接

```
new cdScp({
      path: resolve(__dirname, "../dist/"), //需上传路径（可以和output保持一致）
      ssh: {
        host: "xxxxx",
        port:'xxx',
        username: "xxx",
        password: "xxxx",
        path: "~"
      }
    })
```

### 通过私钥

```
new cdScp({
      path: resolve(__dirname, "../dist/"), //需上传路径（可以和output保持一致）
      ssh: {
        host: "xxx",
        port:'xxx',
        username: "xxx",
        privateKey: fs.readFileSync('私钥地址'),
        passphrase: "私钥密码",
        path: "~"}
    })
```

# 结语
因为依赖的库（scp.js）是用基于sftp，请确认服务器是否支持，很多公司会禁用sftp上传，所以如果需要用到此插件，需要靠其他手段，例如接口上传等等；
目前该插件已上传到npm，可以直接install使用：[cdScp--npm](https://www.npmjs.com/package/cd-scp),[cdScp--github](https://github.com/CDzhou/cd-scp)








