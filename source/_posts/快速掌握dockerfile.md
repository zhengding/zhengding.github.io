---
title: 快速掌握Dockerfile
data:  2017-12-12 08:58:01
tag:
- Docker
- Dockerfile
categories: Docker
---

转载自[快速掌握Dockerfile][1]
## docker镜像的简单剖析
docker的images,我们可以理解为积木, 一层一层往上搭, 最后完成一个工程化的大项目.   
在最初,docker实际上,只有一个静态的image(Ps: read-only). 相当于只能读, 所以, 你所有的改动并不会影响到原来的image上, 只会一层一层的叠加, 比如, 你在Ubuntu的image上面, 再接一层nodeJS的image. 实际上的结果是, 两个image叠加起来.
这里放一张 **the Docker book**的说明图:
![][image-1]
docker 在下载image的时候,会在/var/lib/docker目录下创建相关的image 目录. 而运行的container则会放在/var/lib/docker/containers中.
另外,docker中的image,是存储在docker仓库. 现在,我们通过快速创建自已的仓库来仔细了解一下docker是怎样拥有这样一个完善的生态的.

## docker 仓库
首先, 要想拥有自己的docker 仓库, 你得有一个自己的docker账号.so, 那就想apply 一个呗. 在docker hub上面注册一下自己的账号就行.

## 登录指令
在docker中,不仅支持web查看docker中的内容, 而且还支持使用命令行登录.

```bash
// 登录到docker
docker login // 然后输入账户密码就ok了
// 使用完毕,想要登出
docker logout
```

实际上,docker会将你的认证信息存放在. /.docker/config.json当中。

## images 常用命令
如果浏览了上面的docker仓库, 会发现在一个repository里面会存在很多images, 比如ubuntu的repository.不同的images发布,代表的都是特定的版本系统. 所以,在拉取的时候,需要额外注意一下你需要的指定docker images.

## images的拉取
在container中,我们讲过,使用docker run的时候, 你会发现如果你的images里面没有存在指定的image时, docker会主动去docker hub里面找,然后下载,并且自动运行.

```bash
// 运行最新版的ubuntu image
docker run -t -i ubuntu:latest
```

如果,你想自己手动下载images的话,可以直接pull

```bash
// 手动拉取images
docker pull ubuntu:latest
// 拉取12.04版本的ubuntu images
docker pull ubuntu:12.04
```

如果在拉取的时候,想知道这个image是否是真正存在的话,就可以使用.docker 提供的搜索指令.

## 搜索指定docker
在docker中,可以使用自带的search命令,搜索所有含有指定term的image. 相当于js中的search 方法.

```bash
// 搜索name中含有demo的image
docker search demo
// 结果为: 名字. 通常为: author/image_name . 通常搜索的就是这个
// 描述: 就是一段文字描述
NAME  DESCRIPTION  STARS OFFICIAL   AUTOMATED
```

查到之后,我们就可以使用pull将指定的库,拉下来了.

## 创建自己的image
上面说过, contianer是copy image运行的进程容器,image是不会变的read-only 块. 但是,如果我们在container里面, 改动了一些设置,比如,下载了node, 而且,我想要保存我这次改动, 以至于,下次我想重新,启动该image时, 他已经具备了node.

```bash
// 现在我再ubuntu:latest上面安装了node
// 伪代码
npm install node -g
```

docker提供了一个非常快捷的方式就是创建自己的docker image. 使用docker commit.

```bash
// 查看刚才改动的container ID
docker ps -a -q -l
// 得到 docker_id, 提交到自己的库中
docker commit docker_id villainHR/node
// 之后会返回新的image id
```

需要注意,docker commit提交的并不是一个整体的100+MB的ubuntu+node. 他只会将两个仓库的差异提交,比如原来image和新的image比起来,就是多了一个npm install node -g命令.

## 使用Dockerfile
Dockerfile是为了迅速的构建image而出现的. 他与docker commit 的区别在于. 能够迅速的更替历史image 命令. 比如,我们以前下载的npm是version 2, 现在想要更换为npm@3的话,则难度就不是一般的了. 但是,如果我们能够像写命令一样将下载的配置命令下载Dockerfile里面, 那么以后我们想要更换版本,就是很方便的啦.  
ok, 现在我们来了解一下Dockerfile是怎样的运行的.
dockerfile demo讲解
这里,我们利用dockerfile 来搭建一个简单的webServer. 首先创建一个你自己的dockerfile目录

```bash
mkdir first_docker
cd first_docker
touch Dockerfile
```

然后, 确保你有ubuntu:latest image.因为, 接下来我们就是基于它,来搭建我们的server.

```bash
first dockerfile demo
FROM ubuntu:latest
设置该dockerfile的作者和联系邮箱
MAINTAINER Jimmy "villainhr@gmail.com"
开始配置环境, 下载apt-get,生成index.html的文件
RUN apt-get update && apt-get install -y nginx
RUN echo 'first demo' > /usr/share/nginx/html/index.html
暴露server的port
EXPOSE 80
```

说一下上面的命令内涵.
•	FROM: 用来指定第一层image, 这是必须有的. 并且指定的image是存在在你的computer中. 相当于是 docker run.  

•	RUN: 这是用来在container中,做出相应的修改. 相当于 修改+docker commit xxx. 给原来的image加上一层layer. 然后, docker会在你commit新一层之后,重新docker run你最新改动过的image  

•	MAINTAINER: 设置作者和联系邮箱.其实就是docker commit 后面的Name参数. 而且加上了联系邮箱. 这是在dockerfile 运行完后,会自动添加到image上的.  

•	EXPOSE: 用来给最新的container 设置与外部交流的port  

上面简单的介绍了基本的dockerfile的命令. 不过, 这尼玛太简单了,不符合我们一贯追求到底的风格.  
这里, 我们在来细说一下RUN这个命令. 实际上, 这应该是dockerfile的关键. RUN的原理很简单, 就是commit + run. 先创建一个新的image 然后 在这个基础上将原有的container替换为新的,如果某一步的RUN发生错误,则container会停在那个阶段, 这样,你可以直接进入该container去查看,你那一步的RUN发生了什么BUG。 另外, 使用RUN的时候, 需要注意, 由于,dockerfile是由上到下解析的, 比如你一开始FROM ubuntu的image, 那么此时的环境是停留在ubuntu的shell中的.  
比如:

```bash
RUN touch demo.js
// 等同于
/bin/sh -c touch demo.js
```

所以, 如果你调用的image 并没有shell的话, 那么久需要使用exec调用系统shell 来执行命令.

```bash
// 调用系统的shell来运行, 实际上就是 exec xxx xxx xxx.
RUN ["npm","install","node"]
```

## 运行dockerfile
上面的dockerfile文件配置好了之后,就轮到我们运行dockerfile.直接运行docker build即可.

```bash
// 注意后面的".", 用来指定搜索dockerfile文件的路径. 
docker build -t="jimmy/first_dockerfile" .
```

说一下docker build的指令吧.

```bash
// 基本格式为:
docker build -t="repository/name:tag"  directory
// -t用来指定生成的image的name,比如仓库,image的名字以及他的tag,如果你不指定tag, 那么docker会自动添加latest代替。
// directory 用来相对于当前运行build的目录, 搜索指定的dockerfile.当然,你也可以使用绝对路径了
```

顺利的话,应该就会有, 下列的信息出来.

```bash
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM ubuntu:latest
 ---> c5f1cf30c96b
Step 2 : MAINTAINER jimmy "villainhr@gmai.com"
 ---> Running in 078148a5086a
 ---> 11b061f665d1
Removing intermediate container 078148a5086a
Step 3 : RUN cd /var
 ---> Running in ffd3141e64c8
 ---> a4d7c5303b60
Removing intermediate container ffd3141e64c8
Step 4 : RUN touch demo.js
 ---> Running in c8393a6fcc98
 ---> 109b402b9adc
Removing intermediate container c8393a6fcc98
Step 5 : EXPOSE 80
 ---> Running in 2c064f4bac57
 ---> ff7ad58a5d8a
Removing intermediate container 2c064f4bac57
Successfully built ff7ad58a5d8a
```

然后, 你可以使用docker images查看.就会发现多出来一个image.

## dockerfile cache
上面已经提到过,使用docker build的时候,如果你的dockerfile中的某一步出现问题的话,你生成的image会停留在那一步.当你fix errors时, 重新运行docker build, 此时,docker是不会真的重头来建一遍的,他会使用你改动line的前一个image,然后以此为基点继续向下构建.  
不过,如果你使用缓存的话,他前面的版本id是不会发生改变的.如果你想完整的得到一个新的ID的话,就可以在build的时候,禁用掉cache.

```bash
docker build --no-cache -t="jimmy/first_dockerfile" .
```

不过,该方法是不推荐的. 因为一个非常棒的cache机制,就被你硬生生的cancel. 而且,这也极力不推荐使用该方法进行cache的取消.以为,有些地方,我们完全可以利用cache来加快速度.这就需要使用到ENV关键字.来帮助我们,另外利用cache.  
在讲解ENV之前,先给大家讲解一下docker cache的运行机理.  
（是不是感觉很激动）  
实际上,机理就一句话:ID命中. 因为docker在你每次运行一行命令的时候,会自动生成一个id值.

```bash
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM ubuntu:latest
 ---> c5f1cf30c96b  // 这就是ID值
```

docker借由这个ID值,来判断是否有cache镜像.所以,这里就需要借一下ENV这个比较费的指令,来灵活的帮助我们使用cache.

## 配置化cache
ENV的就是给docker来设置变量的. 基本格式为:

```bash
一个一个的赋值
ENV key value
// demo:
ENV name jimmy
ENV age 18
另外,还可以一起赋值
ENV key=value[...]
// demo:
ENV name=jimmy age=18
```

而通过ENV我们就可以完美的告诉docker 从这里开始,你就不能使用cache,自己的重新来.(因为,每条指令都会生成layer并且有独立的id,一旦你更改的ENV,那么从该指令开始id都会发生改变,也就匹配不到缓存了)
看个demo:

```bash
第一个dockerfile
FROM ubuntu:latest
MAINTAINER jimmy "villainhr@gmai.com"
ENV REFRESH first # 这里设置的是refresh=first
RUN cd /var
RUN touch demo.js
EXPOSE 80

// 使用docker build ... 后面就会生成一系列新的id和images
// 现在修改dockerfile
第二个dockerfile
FROM ubuntu:latest
MAINTAINER jimmy "villainhr@gmai.com"
ENV REFRESH second # 这里设置的是refresh=second
RUN cd /var
RUN touch demo.js
EXPOSE 80

// 开始运行docker build... 你会发现,从下面语句开始.
ENV REFRESH second
// 其docker id就已经发生了改变,并且docker 没有了use cache的提示.说明,下面就没有命中缓存了. 所以,如果你想在某一段不使用缓存,只需要将ENV后面的value改变即可.
```

创建完后, 我们可以使用docker history,查看一下刚才创建的image的整个流程.

```bash
// 查看image创建的过程
docker history jimmy/first_dockerfile 
// 输出的结果为:
2322ddc85cc3        10 hours ago        /bin/sh -c #(nop) EXPOSE 80/tcp                 0 B                 
b39397abc7aa        10 hours ago        /bin/sh -c touch demo.js                        0 B                 
3c9a4daf4c42        10 hours ago        /bin/sh -c cd /var                              0 B                 
b1c2f890a262        10 hours ago        /bin/sh -c #(nop) ENV REFRESH=second            0 B                 
2cf0ee3c373c        10 hours ago        /bin/sh -c #(nop) MAINTAINER jimmy "villainhr   0 B              
```

俺的目的,其实是想让你们看看,docker在每一层是怎么执行的--/bin/sh. 了解了之后,我们就继续了.

## docker container的接口暴露
上面通过dockerfile 已经暴露了一个80接口,用来和外部通信。 不过,如果我们没有使用EXPOSE暴露接口的话, 那应该怎么做呢？  
我们可以直接在外部运行docker image, 手动指定暴露的端口.

```bash
同样,暴露80端口给外部交互
docker run -d -p 80 --name demo jimmy/node \
node -jimmy app.js

-d是daemon的意思
-p 80 表示暴露80的port给外部
node -jimmy app.js 表示在jimmy/node image里面运行的指令
```

这里, 我们需要额外了解一下80端口的开启. docker 其实是在底层上面,虚拟化了存储. 并且,docker在运行的时候,会自动向主机要一个ip(假的), 相当于,有了自己的host. (这不就一个主机吗？)  
这里我们开启的80端口,是docker在内部虚拟开启的, 他会从32768 到 61000端口之间,随机抽一个映射到docker开启的80端口上, 依此来和外部进行真正的交互(膜拜///).

```bash
使用docker ps -l 来查看开启情况
docker ps -l
得到: 只截取了一部分.
0.0.0.0:49154->80 tcp
或者指定查看docker端口开启情况
docker port c96f2c18bb64 80 // ID也可以使用name代替
返回:
 0.0.0.0:49154
```

## 手动指定端口
如果你不想让docker决定的绑定的接口是哪一个,ok, 你可以自己指定.

```bash
手动指定端口
指定docker的8080连接到container暴露的80端口
docker run -d -p 8080:80 --name demo jimmy/node \
node -jimmy app.js
甚至你也可以指定ip+port
指定docker的127.0.0.1:8080连接container的80
docker run -d -p 127.0.0.1:8080:80 --name demo jimmy/node \
node -jimmy app.js
```

## 利用EXPOSE
在写dockerfile的时候,我们已经了解了,使用EXPOSE可以完美的实现端口的暴露. 但如果,我们在dockerfile里面暴露多个port的话,那么-p的参数,感觉有点鸡肋啊喂  
不过,现在我们可以使用-P(注意是大写). 来手动开启所有在dockerfile中,通过EXPOSE暴露的端口.

```bash
docker run -d -P --name demo jimmy/node \
node -jimmy app.js
```

## 外部访问
通过端口开启之后,我们就可以间接的访问docker的路由, 来访问在docker里面开启的端口了.

```bash
假如上面我们通过dockre暴露的端口是34251的话,就可以在docker环境外访问了.
ping localhost:34251
```

## dockerfile常用指令
自动化运行CMD
你是不是已经厌烦了使用docker run 来运行命令了呢？ 你是不是已经讨厌重复的copy命令运行了呢？  
那么请使用CMD吧.   
CMD的作用是,用来指定当你调其对应的container时, 运行的命令.  
比如在dockerfile中,指定/bin/bash.

```bash
当调起container时,运行/bin/bash
docker run  -t -i jimmy/ubuntu:latest /bin/bash
等同于在dockerfile中指定CMD
CMD ["/bin/bash"]
// 运行docker run
docker run -t -i jimmy/ubuntu:latest
```

不过,如果你在run后面手动指定指令运行的话,会默认覆盖掉CMD提供的命令.  
熟悉了CMD,感觉有种RUN的感觉. 但,这两者的区别还是很大的
•	RUN: 一般是用来给image增加layer来完善image, 他一旦执行完,就和后面的运行没有关系了  

•	CMD: 这个在docker build过程中,是没有半毛钱关系的. 他只和在调用image时,关系比较大  

## 强制运行ENTRYPOINT
这里的ENTRYPOINT和CMD很相似. 可以说,在一定程度上两者可以互相替代,但,两者的实际意义相差还是挺大的.  
ENTRYPOINT的主要功能是强制执行的环境.

```bash
指定ENTRYPOINT为/bin/sh
ENTRYPOINT ["/bin/sh"]
// 然后在build之后,调起container
我们尝试在run后面加上参数:
docker run -t -i jimmy/demo /bin/bash/
// 不出意外的话,会得到一个bug提示:
>>/bin/sh: 0: Can't open /bin/bash/
```

所以, ENTRYPOINT的主要功能实际上是,指定了内部运行命令的解析器. 而使用docker run添加的命令,会被当做参数添加给ENTRYPOINT.

```bash
已经指定了ENTRYPOINT ["/bin/sh"]
运行docker run
docker run -t -i jimmy/demo /bin/bash/
实际上相当于(不出错才怪嘞...)
/bin/sh /bin/bash/
```

另外,我们还可以使用CMD配合ENTRYPOINT写成默认参数的效果.

```bash
默认执行 /bin/bash default.sh
ENTRYPOINT ["/bin/bash"]
CMD ["default.sh"]
如果你在docker run中指定了参数的话,则CMD会默认被代替 
docker run jimmy/demo sam.sh
```

不过,CMD和ENTRYPOINT都只能在dockerfile里面出现一次.

## 指定运行目录WORKDIR
既然,我们能够在dockerfile里面运行指定的命令。 但,有时,我们仅仅是想在不同的目录中执行不同的命令. 那,在dockerfile中,如何做到灵活的目录切换呢？  
那就得使用docker提供的WORKDIR命令了.

```bash
在/var/data里面创建data.js
WORKDIR /var/data
RUN touch data.js
然后在/etc 下创建data.conf文件
WORKDIR /etc
RUN touch data.conf
```

并且当你在使用docker run时,他也会停留在使用WORKDIR指定的目录中.

## 环境变量的设置ENV
ENV在dockerfile里面的用处,应该算是灰常大的. 什么灵活更新,什么变量设置,什么更改全局变量等. 都是so easy.   
那ENV到底是用来干嘛的?  
答: 就是用来设置变量的啊喂. 只是他是设置全局变量.  
比如像PATH神马的之类的.

```bash
设置一个DATA的全局变量.
ENV DATA=jimmy
```

ENV最独特之处在于,他所设置的变量,会在你运行的时候生效.即,如果你修改了PATH,他也会在container中立即生效.

```bash
修改环境变量
ENV PATH=$PATH:/user/bin
// 现在进入到运行的container中
echo $PATH
>> /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/data
```

## 挂载通用盘VOLUME
在说里面正式的内容之前,我们先来说一下,什么叫做VOLUME. 说人话吧,VOLUME叫做数据卷, 相当于通用盘一样的东西. 他其实也是一个存储装置,我们就把他叫做硬盘吧. 这个硬盘不普通,有\>1的外接口.(说人话) 每一个外接口,都可以接入到一个操作系统里面. 即,实现了多个系统的数据共享.  
一句话:
> VOLUME就是一个数据共享盘
而,docker秉承着,虚拟储存idea, 想下面idea践行到底.
> wirte once, run anywhere
(感觉,在哪见过)  
所以, dockerfile提供了一个VOLUME的指令,能够让我们指定数据卷的位置.

```bash
指定/opt/data为数据卷
VOLUME ["/opt/data"]
指定多个目录为数据卷/opt/data, /opt/project
VOLUME ["/opt/data","/opt/project"]
```

当然,关于数据卷的操作,肯定不止挂载这一点,还有迁移,备份等等,相关操作. 具体,可以参考: Docker VOLUME

## 添加外部文件ADD
有时,我们仅仅是想将外部文件copy到container中,docker有办法吗？  
nonsense  
docker 提供了ADD命令,来帮助我们完成文件的添加. 不过,这里ADD有点限制, 即, 你添加的文件或者目录,只能在docker build运行的目录下, 因为,这是docker在调起container的时候,只将该目录放进了daemon(尴尬)

```bash
现假设,docker build运行的目录为: /data
// 只能添加指定目录下
// 将/data/sam.js 添加到image中的/opt/node/sam.js
// 如果存在该文件,则不会被覆盖
ADD sam.js /opt/node/
添加文件,还可以使用通配符
// 将所有的js文件,添加到node目录下
ADD *.js /opt/node/
如果destination不是绝对路径,则相对于最近的WORKDIR
// 如果最近的WORKDIR为/var
// 则下列添加的路径为/var/opt/node
ADD *.js opt/node/
```

当文件被添加到指定目录中时,该文件的权限是755,并且UID和GID都是0.  
ADD 还支持url添加,以及文件自动解压.

```bash
使用url添加
// 将指定路由的文件放到根目录当中
ADD http://example.com/foobar /
自动解压tar.gz文件
// 将文件解压过后放在指定目录中
ADD latest.tar.gz /var/www/wordpress/
```

## 纯粹的COPY
COPY和ADD非常类似. 我们可以做个类比:
ADD 包含 COPY
COPY做的事情比不上ADD, 他比ADD少了解压缩和URL下载的功能. 不过,他耗费的性能比较少,他只做纯粹的添加和下载.他的结构和ADD一毛一样. 不过, 有一点,COPY的时候,如果遇到目录不存在的情况下,COPY会自动创建

```bash
COPY file.js /opt/data/
```

## 添加个人信息LABEL
顾名思义,使用LABEL就是给你的image打上独一无二的标签.让别人能够了解,这个Image是属于你的. 又或是,用来提醒你自己,这个image现在处于哪一个版本状态.

```bash
设置自己的label
LABEL owner="jimmy" version="0.0.1"
```

在创建完image之后, 我们可以使用docker inspect来查看我们已经打的LABEL

```bash
docker inspect jimmy/node
...
labels:{
owner:"jimmy",
version:"0.0.1"
}
...
```

本人觉得, 这个指令其实真的,有时, 母鸡用到什么地方去...  
并且,书写的时候,最好多个连着写,因为这样只会增加一层image.(image的层数是有限制的)

## 参数形式ARG
这是docker提供的另外一个,让我有点懵逼的命令. 他的实际效果和ENV的区别可以趋近于无。

```bash
使用ARG定义变量
ARG buildno
设置默认值
ARG user1=someuser
```

当然,我们可以在命令中,手动指定替换.

```bash
在dockerfile定义了默认变量
ARG user=jimy
在运行时,进行手动替换
docker build --build-arg user=sam -t jimmy/demo .
```

上面说了ARG和ENV比较类似,不过,里面的区别还是有的. 即, ARG只能用在docker build的阶段, 并且不会被保存在image中,这就是和ENV的区别.

## 模板image之ONBUILD
因为dockerfile的构建的层数有限制,所以,这也带给了我们一些麻烦, 如果搭建的环境过多,则会造成写到一半,发现dockerfile已经full. 这时候, 就轮到ONBUILD出场了. ONBUILD的作用在于,他可以完美的实现模板image的搭建.   
ONBUILD的主要作用在于,他定义的命令,可以在子dockerfile中使用.(md... 好绕口)

```bash
使用ONBUILD 默认下载Apache
ONBUILD RUN apt-get update && apt-get install -y apache2

// 然后运行docker file 会得到下列结果
 Step 3 : ONBUILD RUN apt-get update && apt-get install -y apache2
   ---> Running in 0e117f6ea4ba
   ---> a79983575b8

//然后生成一个新的image,我们这里暂且叫他jimmy/demo
```

接下来,我们再来写一个dockerfile

```bash
这里继承上面的jimmy/demo
FROM jimmy/demo:latest
ENV SEX=001

// 运行上面的dockerfile,得到:
Step 0 : FROM jimmy/demo
Executing 1 build triggers
   Step onbuild-0 : ADD . /var/www/
   ---> 1a018213a59d
   ---> 1a018213a59d
 Step 1: ENV SEX=001
 ...
```

细心的童鞋可以发现这一条命令:

```bash
 Step onbuild-0 : RUN apt-get update && apt-get install -y apache2
   ---> 1a018213a59d
   ---> 1a018213a59d
```

他居然在这里自动运行了. 所以,我们可以将ONBUILD命令理解为模板命令. 即,子dockerfile里面运行时同样生效(这里,我没有说grandchildren的事).  
但ONBUILD只能往下延伸一级. 相当于你用ONBUILD定义的命令,有两次有效次数,一次在build原来Image时,已经用掉了. 所以, 另外一次(在子dockerfile中使用)用掉了之后就无效了. grandchildren dockerfile就没法使用了.




[1]:	https://segmentfault.com/a/1190000006186977?utm_source=tuicool&utm_medium=referral

[image-1]:	http://p09eeagrw.bkt.clouddn.com/59773500.jpg