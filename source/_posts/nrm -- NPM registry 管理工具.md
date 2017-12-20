---
title: nrm -- NPM registry 管理工具
data:  2017-12-13 16:07:01
tag:
-  NPM
- nrm
categories: 前端
---

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

