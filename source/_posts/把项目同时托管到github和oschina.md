---
title: 把项目同时托管到github和oschina
data:  2017-12-20 08:07:01
tag:
-  Git
categories: Git
---

## 1、添加远程地址，给地址命名来区分

```bash
git remote add oschina https://git.oschina.net/xxx/xxx.git
或者
git remote add github https://github.com/xxx/xxx.git
```

## 2、查看远程地址

```bash
git remote -v
```

## 3、然后push

```bash
git push -u oschina
或者
git push -u github
```

