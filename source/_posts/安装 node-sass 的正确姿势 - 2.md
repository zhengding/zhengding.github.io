---
title: 安装 node-sass 的正确姿势
date: 2017-12-1 13:27:58
tags: 
- node-sass
categories: 前端
---

安装 node-sass 的时候总是会各种不成功，今天我琢磨了一会儿总算知道要怎么解决了。
首先要知道的是，安装 node-sass 时在 node scripts/install 阶段会从 github.com 上下载一个 .node 文件，大部分安装不成功的原因都源自这里，因为 GitHub Releases 里的文件都托管在 s3.amazonaws.com 上面，而这个网址在国内总是网络不稳定，所以我们需要通过第三方服务器下载这个文件。（顺带一提，你可以看看这个好玩的 commit）
## 方法一：使用淘宝镜像
macOS 系统直接运行下面的命令即可：
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass
我们一般更希望能跨平台、并且直接使用 npm install 安装所有依赖，所以我的做法是在项目内添加一个 .npmrc 文件：

```bash
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
phantomjs_cdnurl=https://npm.taobao.org/mirrors/phantomjs/
electron_mirror=https://npm.taobao.org/mirrors/electron/
registry=https://registry.npm.taobao.org
```

这样使用 npm install 安装 node-sass、electron 和 phantomjs 时都能自动从淘宝源上下载，但是在使用 npm publish 的时候要把 registry 这一行给注释掉，否则就会发布到淘宝源上去了。
## 方法二：使用梯子
假设你的梯子在你本地机器上开启了一个第三方服务器 127.0.0.1:1080，那么只需按照下面的方法配置一下就能正常安装 node-sass 了（如果你开启的是 PAC 模式而不是全局模式，那还需要将 s3.amazonaws.com加入 PAC 列表）：

```bash
npm config set proxy http://127.0.0.1:1080
npm i node-sass

下载完成后删除 http 代理
npm config delete proxy
```

嗯，这样下来就能正常安装了。


