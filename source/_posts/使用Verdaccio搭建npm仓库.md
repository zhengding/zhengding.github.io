title: 快速掌握Dockerfile
data:  2017-12-12 08:58:01
tag:

​	- Docker

​	- Dockerfile

categories: Docker



# 使用Verdaccio搭建npm仓库

Verdaccio是一个轻量级的私有NPM的Registry（从[Sinopia](https://github.com/rlidwka/sinopia)fork过来的，sinopia最后一次更新是在几年前了）。最开始是打算使用cnpmjs这个来搭建私有的npm仓库但是搭建完成之后存在一些问题，所以使用了Sinopia，本文记录下来搭建的过程。

![verdaccio](http://blog.devzeng.com/images/verdaccio/logo.png)

### 直接安装

```
npm install verdaccio -g

```

安装完成之后直接执行命令即可完成启动：

`verdaccio`

对于服务器上面部署可以使用pm2

更多关于PM2的用法的可以参考：`https://wohugb.gitbooks.io/pm2`

### 使用Docker安装

由于之前搭建Nexus的时候安装了Docker的环境，刚好verdaccio支持Docker，所以直接使用Docker来安装了。

#### 1、创建配置文件

```
mkdir -p /home/docker
git clone https://github.com/verdaccio/docker-examples
mv docker-local-storage-volume /home/docker/verdaccio

```

#### 2、修改配置文件

（1）修改`conf/config.yaml`

```
# 使用淘宝的cnpmjs镜像，加速
uplinks:
  npmjs:
    url: https://registry.npm.taobao.org/

```

（2）修改`conf/htpasswd`

到`http://tool.oschina.net/htpasswd`生成需要的账号密码

（3）删除`storage`下面的文件

#### 3、创建并运行容器

```
docker run --name verdaccio -d -v /home/docker/verdaccio:/verdaccio -p 4873:4873 verdaccio/verdaccio:latest

```

通过`docker logs verdaccio`查看日志发现存在权限问题，是由于使用了挂载的数据卷和容器内部的权限不一致的问题，需要修改下权限：

（1）进入容器

```
docker exec -it verdaccio sh

```

（2）查看用户ID

```
whoami & id

```

（3）修改文件夹权限

```
sudo chown -R 200 verdaccio

```

### 客户端配置

启动容器之后访问`http://localhost:4873`出现如下界面表示安装成功：

![verdaccio](http://blog.devzeng.com/images/verdaccio/demo.png)

（1）配置使用私有的镜像

```
npm set registry http://localhost:4873

```

（2）登录

```
npm adduser --registry http://localhost:4873

```

（3）发布

```
npm publish --registry http://localhost:4873

```

### 参考资料

1、[verdaccio官网](http://www.verdaccio.org/)

2、[使用Sinopia搭建私有的npm仓库](https://github.com/Pines-Cheng/blog/issues/1)

3、[CentOS6.x 安装 Docker 和 Docker Compose](http://blog.csdn.net/kinginblue/article/details/73527832)

4、[谈谈 Docker Volume 之权限管理（一）](https://yq.aliyun.com/articles/53990)