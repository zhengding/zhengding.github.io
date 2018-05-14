---
title: Docker常用命令
data:  2017-12-12 14:07:01
tag:
- Docker
categories: Docker
---

1、查看容器

```shell
docker ps #查看正在运行容器
docker ps -a #查看所有容器
```

2、停止容器  

```shell
docker stop containerIs #停止容器
docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }') #停止所有已退出容器
```

3、删除容器

```shell
docker rm containerIs #删除容器
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }') #删除所有已退出容器
```

或

```shell
docker rm -v $(docker ps -aq -f status=exited)
```

4、删除镜像

```shell
docker rmi imageId #删除镜像
docker rmi $(docker images | grep "none" | awk '{print $3}') #删除所有为none的镜像
```

5、启动容器

```shell
docker run -d -p 8080:8080 --restart=always containerId
-d: #后台启动
-p: #映射端口
--restart=always: #自动重启
--name="zhengding/oracle": #为容器指定一个名称
--dns 8.8.8.8 #指定容器使用的DNS服务器: 默认和宿主一致
-e username="ritchie": #设置环境变量
--env-file=[]: #从指定文件读入环境变量
--net="bridge": #指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型
```

6、进入容器

```shell
docker exec -it fc873 /bin/bash
```

7、查看日志

```shell
docker logs fc873 
```

8、标记本地镜像，将其归入某一仓库

```Bash
docker tag ubuntu:15.10 runoob/ubuntu:v3
```

9、使用Dockerfile创建镜像

使用当前目录的Dockerfile创建镜像。

```
docker build -t runoob/ubuntu:v1 . 
```

使用URL **github.com/creack/docker-firefox** 的 Dockerfile 创建镜像。

```
docker build github.com/creack/docker-firefox
```

10、加载镜像

```
docker load --input runoob/ubuntu:v1
```

11、进入容器之后进行编辑ssh文件，如果没有ssh,需要先安装：
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

12、编辑ssh配置文件：

```shell
$sudo vim/etc/ssh/sshd_config
```

PermitRootLogin without-password 改为 PermitRootLogin yes`
`PasswordAuthentication yes 改为 PasswordAuthentication no

13、重启服务：

```shell
$ sudo service ssh restart
```

14、设置ssh密码：

```shell
$ passwd root
Enter new UNIX password:   
Retype new UNIX password:   
passwd: password updated successfully
```

11、如何在Docker容器内外互相拷贝数据？

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

 12、自动清理镜像容器

```bash
docker system prune
```

13、查看当前容器资源占用i情况

```bash
docker stats
```

