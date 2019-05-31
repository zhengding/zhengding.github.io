---
title: npm常用命令
data:  2017-12-13 16:07:01
tag:
- npm
- nrm
categories: 前端
---

# 一、常用命令

```bash
#查看包信息
npm list -g gulp  
#安装并查看安装信息
npm -loglevel info install -g gulp  
#发布到https://registry.npmjs.org/
npm --registry https://registry.npmjs.org/ publish  
#设置npm库
npm config set registry https://registry.npm.taobao.org/
npm config set registry http://registry.npmjs.org 
#查看当前npm库
cat ~/.npmrc
#登录
npm adduser –-registry http://192.168.101.30:10005/repository/npm-repo-hosted/
```



# 二、nrm的使用

开发的npm registry 管理工具 nrm,  能够查看和切换当前使用的registry, 最近NPM经常 down 掉, 这个还是很有用的哈哈

## Install

```bash
$ npm install -g nrm
```

## Example

```bash
$ nrm ls
* npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
  eu ----- http://registry.npmjs.eu/
  au ----- http://registry.npmjs.org.au/
  sl ----- http://npm.strongloop.com/
  nj ----- https://registry.nodejitsu.com/
```

```bash
$ nrm use cnpm //switch registry to cnpm
	Registry has been set to: http://r.cnpmjs.org/
```

```bash
cmd
nrm help // show help
nrm list // show all registries
nrm use cnpm // switch to cnpm
nrm home // go to a registry home page
```



