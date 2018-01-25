---
title: Docker常用命令
data:  2017-12-12 14:07:01
tag:
- Docker
categories: Docker
---

1、查看所有容器

```shell
docker ps -a
```

2、停止容器  

```shell
docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }')
```

3、删除容器

```shell
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') 
```

或

```shell
docker rm -v $(docker ps -aq -f status=exited)
```

4、删除镜像

```shell
docker rmi $(docker images | grep "none" | awk '{print $3}')
```

5、进入容器

```shell
docker exec -it fc873 /bin/bash
```

6、进入容器之后进行编辑ssh文件，如果没有ssh,需要先安装：
安装ssh-client命令：

```shell
sudo apt-get install openssh-client  
```

安装ssh-server命令：

```Shell
sudo apt-get install openssh-server
```

安装完成以后，先启动服务：

```shell
sudo /etc/init.d/ssh start  
```

启动后，可以通过“ps -e|grep ssh”查看是否正确启动。

7、编辑ssh配置文件：

```shell
$sudo vim/etc/ssh/sshd_config
```

PermitRootLogin without-password 改为 PermitRootLogin yes`
`PasswordAuthentication yes 改为 PasswordAuthentication no

8、重启服务：

```shell
$ sudo service ssh restart
```

9、设置ssh密码：

```shell
$ passwd root
Enter new UNIX password:   
Retype new UNIX password:   
passwd: password updated successfully
```

10、如何在Docker容器内外互相拷贝数据？

​	该 **cp**命令可以用来复制文件。一个特定的文件可以复制，如：

```
docker cp foo.txt mycontainer:/foo.txt
docker cp mycontainer:/foo.txt foo.txt
```

文件夹中包含的多个文件**src**可以使用以下命令复制到**target**文件夹中：

```
docker cp src/. mycontainer:/target
docker cp mycontainer:/src/. target
```

 