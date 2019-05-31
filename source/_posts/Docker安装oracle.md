---
title: Docker安装oracle
data:  2018-01-04 17:57:01
tag:
- Docker
- oracle
categories: 数据库
---

## 常用命令
将数据库文件生成镜像
docker load —input docker-oracle-xe-11g.tar
生成容器
docker run -d -p 1522:1521 f785
进入容器内部
docker exec -it fc873 /bin/bash
查看数据库状态
lsnrctl status(BLOCKED非正常)
配置文件路径
/u01/app/oracle/product/11.2.0/xe/network/admin
安装vim
apt-get update
apt-get install vim

## 常见错误
1、Connection to @192.168.2.156 failed.
[66000]()[12528]() Listener refused the connection with the following error:
ORA-12528, TNS:listener: all appropriate instances are blocking new connections  



