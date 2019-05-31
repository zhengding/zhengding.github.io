---
layout: ssh
title: 直接登录docker容器
date: 2019-03-29 09:58:15
tag:
- Docker
- ssh
categories: Docker
---

docker容器，我们想直接通过ssh链接docker容器，而不是通过宿主机进入的方式

首先，先了解一下docker，[docker 命令](http://www.fecshop.com/topic/591)

启动docker，进入docker容器

1.如果是守护态容器，可以通过下面的方式进入：

```
docker exec -it 42d099e3fdca /bin/bash （3c0498ea5254是CONTAINER ID）
```

2.进入docker后，使用passwd密码来修改密码（如提示没有这个命令行使用`yum install passwd`安装）：

```
passwd
    xxx密码
    xxx确认密码
```

3.安装Openssh（docker 容器中执行）

```
sudo yum -y install openssh-server
$ sudo yum -y install openssh-clients
```

修改SSH配置文件以下选项，去掉#注释，将四个选项启用：

vi /etc/ssh/sshd_config

```
RSAAuthentication yes #启用 RSA 认证
PubkeyAuthentication yes #启用公钥私钥配对认证方式
AuthorizedKeysFile .ssh/authorized_keys #公钥文件路径（和上面生成的文件同）
PermitRootLogin yes #root能使用ssh登录
```

重启ssh服务，并设置开机启动：

```
service sshd restart
$ chkconfig sshd on
```

4.退出容器并保存更改

使用exit命令或者ctrl+C来退出当前运行的容器：

```
[root@ffe81683c404 /]# exit
```

注意：上面`ffe81683c404`是容器的ID，退出后用于保存的唯一ID。

当结束后，我们使用 exit 来退出

现在我们的容器已经被我们改变了，使用 `docker commit` 命令，将当前的容器生成镜像。

```
sudo docker commit -m 'install openssh' -a 'Docker Newbee' ffe81683c404  centos6-jdk7:ssh
4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c
```

`-m`:来指定提交的说明信息，跟我们使用的版本控制工具一样

`-a` 可以指定更新的用户信息

`ffe81683c404`： 创建镜像的容器的ID，就是上面的容器id，也就是我们刚才进入的容器id

`centos6-jdk7:ssh`: 目标镜像的仓库名和 tag 信息，冒号前面部分是生成的镜像的仓库名字，冒号后面是镜像tag

创建成功后会返回这个镜像的 ID 信息，查看镜像

```
docker images
```

5.启动新的容器并打通22端口

将新的镜像启动，并将docker服务器的50001端口映射到容器的22端口上：

```
docker run -d -p 2222:22 centos6-jdk7:ssh /usr/sbin/sshd -D
```

然后用xshell连接，

`ip`: 为宿主主机的ip，而不是docker容器的ip

```
端口`:就是上面的`2222
```

`用户名`： root

`密码`： 就是上面password部分设置的密码

即可连接成功

6.关掉该容器

在宿主机通过 `docker stop 容器id` 即可关闭

启动容器 `docker start 容器id`

启动后，就可以用xshell进行连接。