---
title: Mac系统安装PHP7教程
data:  2017-12-5 16:21:01
tag:
- Mac
- PHP7
categories: PHP
---

安装教程：
## 1.首先我们需要安装Homebrew
一条命令完美安装：[http://brew.sh/index_zh-cn.html][1]
## 2.终端输入以下命令

```bash
// 创建目录,如果你没有创建过该目录
sudo mkdir /usr/local/var
sudo chmod 777 /usr/local/var
//修改成你自己的用户名和组,如果你没有创建过该目录
sudo mkdir /usr/local/sbin/
sudo chown -R <username>:<group> /usr/local/sbin//由于我本身一直在使用5.6版本，故上述步骤省略，下面进入正题
//添加PHP库
brew tap homebrew/dupes
brew tap homebrew/versions
brew tap homebrew/homebrew-php
//关闭老版本的PHP56或55或更早版本 进程
brew unlink php56
//开始安装PHP7
brew install php70
//开启PHP70进程
brew link php70
//输入命令，查看是否成功
php -v
//成功后显示下面信息
PHP 7.0.8 (cli) (built: Jul 13 2016 15:19:21) ( NTS )
Copyright (c) 1997-2016 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
```

注：遇未生成libphp7.so

```bash
brew install php70 --with-apxs2  --with-apache --with-gmp --with-imap --with-tidy --with-debug
```


[1]:	http://brew.sh/index_zh-cn.html