---
title: nexus 上搭建npm本地服务器-1
data:  2018-3-9 16:07:01
tag:
- npm
- nexus
categories: 前端
---

![这里写图片描述](http://img.blog.csdn.net/20160901070831124) 
Nexus作为私库管理最为流行的工具之一，用于包的管理和Docker镜像管理的私库管理场景中非常常用，但同时也可以用于NPM私库等的管理。

# Why Nexus 3

这里整理了为什么使用Nexus 3的一些理由，在做选型的时候可以做一个简单参照。

| 项目              | 详细                                                      |
| ----------------- | --------------------------------------------------------- |
| 为什么使用Nexus 3 | <http://blog.csdn.net/liumiaocn/article/details/62050525> |

# docker版本

本次使用的docker的版本为17.09.0-ce，但是并不限于此版本，其他版本未作验证。

```
[root@angular ~]# docker version
Client:
 Version:      17.09.0-ce
 API version:  1.32
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:39:28 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.09.0-ce
 API version:  1.32 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:45:38 2017
 OS/Arch:      linux/amd64
 Experimental: false
[root@angular ~]#123456789101112131415161718
```

# 下载镜像

```
[root@angular ~]# docker pull liumiaocn/nexus
Using default tag: latest
latest: Pulling from liumiaocn/nexus
d9aaf4d82f24: Pull complete 
1bf6f9237306: Pull complete 
2cb68a4c7b1e: Pull complete 
a81c750844e6: Pull complete 
ea509a1d48c2: Pull complete 
24d48f5e8202: Pull complete 
Digest: sha256:3480d50b7ddfb72fe77ce55c7b5ce0a9aa7b6cfa90886a8f8ddc4a2401f1b7c9
Status: Downloaded newer image for liumiaocn/nexus:latest
[root@angular ~]#123456789101112
```

# 启动Nexus

| 项目         | 详细                           |
| ------------ | ------------------------------ |
| Nexus UI     | 8081                           |
| private repo | 8082                           |
| proxy repo   | 8083                           |
| URL          | <http://192.168.163.151:8081/> |

```
[root@angular ~]# docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus liumiaocn/nexus
d5807c07c10c1f55d4ea85e774529dd6c0daf8833d1e505d4c1f09db00491e87
[root@angular ~]#123
```

# logon

| 项目     | 详细                           |
| -------- | ------------------------------ |
| URL      | <http://192.168.163.151:8081/> |
| 用户名称 | admin                          |
| 用户密码 | admin123                       |

![这里写图片描述](http://img.blog.csdn.net/20171115053834013?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

登陆之后 
![这里写图片描述](http://img.blog.csdn.net/20171115053933763?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 仓库类型

具体仓库类型主要分为hosted/proxy/group三种。具体含义如下：

| 项目   | 详细说明                                   |
| ------ | ------------------------------------------ |
| hosted | 本地存储，提供本地私库功能                 |
| proxy  | 提供代理其他仓库的类型                     |
| group  | 组类型，可以组合多个仓库为一个地址提供服务 |

# 创建blob

创建一个blob用于存放npm相关数据。

| 项目 | 详细说明                        |
| ---- | ------------------------------- |
| Type | File                            |
| Name | npm-repo-blob                   |
| PATH | /nexus-data/blobs/npm-repo-blob |

![这里写图片描述](http://img.blog.csdn.net/20171115054652616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
建议实际项目中各个仓库根据具体情况决定是否要分别创建blob进行对应，此处进行演示就通篇使用一个blob

# 创建priavete仓库

创建一个Hosted的仓库作为private仓库，用于管理非npm社区的第三方包或者自己开发的内容。具体设定信息如下：

| 项目       | 详细说明        |
| ---------- | --------------- |
| 类型       | npm(hosted)     |
| Name       | npm-repo-hosted |
| Blob store | npm-repo-blob   |

其余Default设定即可 
![这里写图片描述](http://img.blog.csdn.net/20171115054813826?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 创建proxy仓库

创建一个proxy仓库，具体设定信息如下：

| 项目           | 详细说明                                                  |
| -------------- | --------------------------------------------------------- |
| 类型           | npm(proxy)                                                |
| Name           | npm-repo-proxy                                            |
| Remote Storage | [https://registry.npmjs.org](https://registry.npmjs.org/) |
| Blob store     | npm-repo-blob                                             |

其余Default设定即可

![这里写图片描述](http://img.blog.csdn.net/20171115055458607?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意：此处的Remote Storage即为可以连接的npm的registry的地址，需要保证正确，并且当前机器能够访问到，比如可以使用curl确认：

```
[root@angular ~]# curl https://registry.npmjs.org
{"db_name":"registry","doc_count":715000,"doc_del_count":344,"update_seq":8090315,"purge_seq":0,"compact_running":false,"disk_size":5368389766,"data_size":3541008450,"instance_start_time":"1510668164237911","disk_format_version":6,"committed_update_seq":8090315}[root@angular ~]# 12
```

注意此处，可以还设定与社区相关的账号和密码等信息进行进一步连接。

# 创建group仓库

创建一个group仓库，具体设定信息如下：

| 项目                | 详细说明        |
| ------------------- | --------------- |
| 类型                | npm(group)      |
| Name                | npm-repo-group  |
| Blob store          | npm-repo-blob   |
| Member repositories | npm-repo-hosted |
| Member repositories | npm-repo-proxy  |

![这里写图片描述](http://img.blog.csdn.net/20171115060119415?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 本地npm设定

## 验证用npm版本

```
[root@angular ~]# npm -v
5.5.1
[root@angular ~]# node -v
v9.1.0
[root@angular ~]# 12345
```

具体安装可以参看：<http://blog.csdn.net/liumiaocn/article/details/78510679>

## 设定.npmrc

可以使用如下命令，也可直接修改.npmrc文件

```
[root@angular ~]# npm config set registry http://192.168.163.151:8081/repository/npm-repo-proxy/
[root@angular ~]#12
```

确认.npmrc

```
[root@angular ~]# cat ~/.npmrc
registry=http://192.168.163.151:8081/repository/npm-repo-proxy/
[root@angular ~]# 123
```

# 确认

至此，Nexus的设定准备基本就绪，可以确认结果了。

## 事前确认

```
[root@angular ~]# npm list -g gulp
/usr/local/npm/node/lib
└── (empty)

[root@angular ~]# 12345
```

## 安装gulp

打开loglevel将其设定为info，在安装的过程中可以看到npm http fetch与新创建的proxy仓库的交互，因为其会将相关的依赖下载到本地nexus的npm私库中。

```
[root@angular ~]# npm -loglevel info install -g gulp
npm info it worked if it ends with ok
npm info using npm@5.5.1
npm info using node@v9.1.0
...
npm http fetch GET 200 http://192.168.163.151:8081/repository/npm-repo-proxy/gulplog/-/gulplog-1.0.0.tgz 4439ms
...
/usr/local/npm/node/bin/gulp -> /usr/local/npm/node/lib/node_modules/gulp/bin/gulp.js
...
npm info lifecycle gulp@3.9.1~postinstall: gulp@3.9.1
+ gulp@3.9.1
added 186 packages in 143.59s
npm info ok 
[root@angular ~]# 
123456789101112131415
```

## 结果确认

```
[root@angular ~]# npm list -g gulp
/usr/local/npm/node/lib
└── gulp@3.9.1 

[root@angular ~]# 12345
```

## group仓库确认

gulp 3.9.1已经通过group仓库可以看到 
![这里写图片描述](http://img.blog.csdn.net/20171115062230237?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## proxy仓库确认

gulp 3.9.1已经通过proxy仓库可以看到

![这里写图片描述](http://img.blog.csdn.net/20171115062251031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bWlhb2Nu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 总结

这篇文章简单介绍了如何使用nexus3进行npm私库设定和使用。