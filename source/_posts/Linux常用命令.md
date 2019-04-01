---
title: Linux常用命令
data:  2018-01-05 11:16:01
tag:
- Linux
categories: 运维
---

1、ssh连接主机

```Bash
ssh root@192.168.2.156 
12345678 
```

2、ssh连接主机copy文件

```Bash
scp root@192.168.2.156:/root/docker-oracle-xe-11g.tar （路径)
```

3、ssh连接主机copy文件夹及其下文件

```Bash
scp -r root@192.168.2.156:/root/docker-oracle-xe-11g.tar （路径)
```

4、chowm 

```Bash
chown -R root:root /usr/meng
```

5、用户、用户组 

```Bash
groupadd  users(users组其实默认已经存在) #创建用户组
useradd    usera #创建用户
cat /etc/group #查看用户组

```

6、ifconfig查看更多

```bash
ifconfig | more
```

然后按 j k 上下移动

7、查看端口占用

```bash
netstat -tunlp 
```

