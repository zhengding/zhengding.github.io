---
title: 把项目同时托管到github和oschina
data:  2017-12-20 08:07:01
tag:
-  Git
categories: Git
---

## 一、

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



## 二、

假设你现在想要增加3个远程库地址，分别为 :

**https://git.oschina.net/shede333/swioslibary.git **

**https://git.oschina.net/shede333/swscrollbar.git **

**https://github.com/shede333/CoreAnimationTestSW.git**

首先，先增加第一个地址 `git remote add origin <url1>` 
然后增加第二个地址 `git remote set-url --add origin <url2>` 
增加第三个地址 `git remote set-url --add origin <url3>` 
….依次类推

这样就完成了添加多个地址到origin库中了， 
以后只要使用`git push origin master` 就可以一次性push到3各库里面了(使用`git push`也可)

## 原理解析

`git remote set-url --add origin` 就是往当前git项目的**config文件**里增加一行记录 
**config文件**打开方式有两种：

1. 使用命令`git config -e`
2. 在当前git项目的根目录下，文件位于 .git/config (.git目录为隐藏文件)

你每执行一次`git remote set-url --add origin` 就会增加一行，如下图：

```
git remote -v`:显示当前所有远程库的详细信息，显示格式为 `远程库名字 url连接(类型)
```