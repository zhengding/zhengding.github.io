---
title: oracle常用命令
data:  2018-01-25 10:39:01
tag:
- oracle
categories: 数据库
typora-copy-images-to: ipic
---

**Oracle中用exp/imp命令参数详解**

**【用 exp 数 据 导 出】：1  将数据库TEST完全导出,用户名system 密码manager 导出到D:\daochu.dmp中**

```sql
exp east/east@xe file=/root/east.dmp full=y
```

exp east/east@xe file=/root/east.dmp full=y
exp east/east file=/root/east.dmp full=y

imp east/east@xe file=east.dmp log=east.dmp full=y ignore=y