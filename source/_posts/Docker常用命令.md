---
title: Docker常用命令
data:  2017-12-12 14:07:01
tag:
- Docker
categories: Docker
---

## 1. 查看docker信息（version、info）

```php
# 查看docker版本
$ docker version

# 显示docker系统的信息
$ docker info
```

## 2、查看容器

```shell
$ docker ps #查看正在运行容器
$ docker ps -a #查看所有容器
```

## 3、启动容器

```shell
$ docker run -d -p 8080:8080 --restart=always containerId
-d: #后台启动
-p: #映射端口
--restart=always: #自动重启
--name="zhengding/oracle": #为容器指定一个名称
--dns 8.8.8.8 #指定容器使用的DNS服务器: 默认和宿主一致
-e username="ritchie": #设置环境变量
--env-file=[]: #从指定文件读入环境变量
--net="bridge": #指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型
```

## 4、进入容器

```shell
$ docker exec -it fc873 /bin/bash
或
$ docker exec -it fc873 /bin/bash
```

## 5. 对容器的操作（rm、rmi、tag、stop、start、kill、logs、diff、top、cp、restart、attach）

```shell
# 删除所有容器
$ docker rm `docker ps -a -q`
#删除所有已退出容器
$ docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') 
$ docker rm -v $(docker ps -aq -f status=exited)
# 删除单个容器; -f, --force=false; -l, --link=false Remove the specified link and not the underlying container; -v, --volumes=false Remove the volumes associated to the container
$ docker rm Name/ID

# 停止、启动、杀死一个容器
$ docker stop Name/ID
#停止所有已退出容器
$ docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }') 
$ docker start Name/ID
$ docker kill Name/ID

#删除镜像
$ docker rmi imageId 
#删除所有为none的镜像
$ docker rmi $(docker images | grep "none" | awk '{print $3}') 

#标记本地镜像，将其归入某一仓库
$ docker tag ubuntu:15.10 runoob/ubuntu:v3

# 从一个容器中取日志; -f, --follow=false Follow log output; -t, --timestamps=false Show timestamps
$ docker logs Name/ID

# 列出一个容器里面被改变的文件或者目录，list列表会显示出三种事件，A 增加的，D 删除的，C 被改变的
$ docker diff Name/ID

# 显示一个运行的容器里面的进程信息
$ docker top Name/ID

# 从容器里面拷贝文件/目录到本地一个路径
$ docker cp Name:/container_path to_path
$ docker cp ID:/container_path to_path

# 重启一个正在运行的容器; -t, --time=10 Number of seconds to try to stop for before killing the container, Default=10
$ docker restart Name/ID

# 附加到一个运行的容器上面; --no-stdin=false Do not attach stdin; --sig-proxy=true Proxify all received signal to the process
$ docker attach ID
```

Note： attach命令允许你查看或者影响一个运行的容器。你可以在同一时间attach同一个容器。你也可以从一个容器中脱离出来，是从CTRL-C。

## 6、使用Dockerfile创建镜像

```shell
#使用当前目录的Dockerfile创建镜像。
$ docker build -t runoob/ubuntu:v1 . 
#使用URL github.com/creack/docker-firefox 的 Dockerfile 创建镜像。
$ docker build github.com/creack/docker-firefox
```

## 7、加载镜像

```shell
$ docker load --input runoob/ubuntu:v1
```

## 8、ssh配置

```shell
#一、进入容器之后进行编辑ssh文件，如果没有ssh,需要先安装：
#安装ssh-client命令：
$ sudo apt-get install openssh-client
#安装ssh-server命令：
$ sudo apt-get install openssh-server
#安装完成以后，先启动服务：
$ sudo /etc/init.d/ssh start
#启动后，可以通过“ps -e|grep ssh”查看是否正确启动。
#二、编辑ssh配置文件：
$ sudo vim/etc/ssh/sshd_config
#PermitRootLogin without-password 改为 PermitRootLogin yesPasswordAuthentication yes 改为 PasswordAuthentication no
#三、重启服务：
$ sudo service ssh restart
#四、设置ssh密码：
$ passwd root
Enter new UNIX password:   
Retype new UNIX password:   
passwd: password updated successfully
```

## 9、自动清理镜像容器

```shell
$ docker system prune
```

## 10、查看当前容器资源占用i情况

```bash
$ docker stats
```

```
<!--more-->
```