---
title: oracle常用命令
data:  2018-01-25 10:39:01
tag:
- oracle
categories: 数据库
typora-copy-images-to: ipic
---

# **Oracle中用命令参数详解**

## 1、exp/imp——导入、导出

【用 exp 数 据 导 出】：1  将数据库TEST完全导出,用户名system 密码manager 导出到D:\daochu.dmp中

```sql
exp east/east@xe file=/root/east.dmp full=y
```

exp east/east@xe file=/root/east.dmp full=y
exp east/east file=/root/east.dmp full=y

imp east/east@xe file=east.dmp log=east.dmp full=y ignore=y

## 2、创建表空间

#### 2.1先创建一个表空间，例如（临时表空间）

create temporary tablespace fitech_temp 

tempfile '/u01/app/oracle/oradata/XE/fitech_temp.dbf' 

size 50m  

autoextend on  

next 50m maxsize 20480m  

extent management local;  

 （永久表空间）

create tablespace fitech_data  

logging  

datafile '/u01/app/oracle/oradata/XE/fitech_data.dbf' 

size 50m  

autoextend on  

next 50m maxsize 20480m  

extent management local;

#### 2.2创建用户并指定表空间

（第一个east为用户名，第二个east为密码）

create user east identified by east 

default tablespace fitech_data  

temporary tablespace fitech_temp;

修改用户表空间

alter user 用户名 default tablespace 表空间名字 ;--创建时候指定表空间

alter user east default tablespace fitech_data  

temporary tablespace fitech_temp;

#### 2.3给用户分配权限

grant connect,resource,dba to east;

#### 2.4使用east用户登录，查看数据库用户正常访问即可

### 3、查看数据库状态

lsnrctl status 









