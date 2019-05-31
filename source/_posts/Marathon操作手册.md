---
title: Marathon操作手册
data:  2018-3-9 17:58:01
tag:
- Docker
- Marathon
categories: Docker
---

​										Marathon操作手册

# 选择Create Application弹出选项



共有两种模式：填写模式、JSON模式

# 一、通用设置

CPU、Memory、Disk Space、Instance根据需要配置

Instance说明：

instances 里 ACTIVE 的数量就是实例的个数，一般都是给1个，特殊情况下给多个，比如说要跑多个容器承载负载多个实例通过统一的访问入口访问  在 Configuration 里有个service endpoint ，host port 是统一的入口

1、填写模式

![img](http://note.youdao.com/src/A563283ACB674E859AE19068E39E2D50)

2、JSON模式

![img](http://note.youdao.com/src/89C1BCBA96C34518BCB03895C807A4D4)

# 二、容器配置

选择bridge模式

![img](http://note.youdao.com/src/9B28F876E74A4823B9DC5468CD35CFD3)

# 三、端口设置

containerport-hostport-serviceport

容器内部端口->宿主机端口->LB端口

访问服务有两个方式，一个是宿主机+宿主机端口

![img](http://note.youdao.com/src/AE5D73A16EB14627A84109995EECFE4C)

# 四、环境变量

![img](http://note.youdao.com/src/39016888C3B94B97BCCA20F5B7AC17B2)

# 五、label设置

1、需要通过lb管理的容器需要配置HAPROXY_GROUP为external

这个label表示要把容器的端口通过load_balance暴露出去，会自动分配一个端口，即service-port

![img](http://note.youdao.com/src/WEBRESOURCEe0e28b220e3711b080362218c9eb6fe1)

2、需要绑定域名的容器配置HAPROXY_0_VHOST为对应域名，数字0根据域名绑定的端口顺序进行设置

仅有一个端口的则配置为0，像gitlab有3个端口443、80、22的需要暴露端口80，该端口是第二个，则设置属性HAPROXY_1_VHOST

![img](http://note.youdao.com/src/0DA6BFED9B9B42DAAE24DD85BB1D4B4D)

# 六、健康检查

默认

![img](http://note.youdao.com/src/CEAFF49AD8D64C7A9709277332280EF2)

# 7、存储卷配置

挂载Volumes，避免重启容器导致数据丢失

![img](http://note.youdao.com/src/2B1C6B0314B84374B22C87831D410BCC)

# 八、其他配置

默认容器重启后平台会从主机集群中自动匹配主机进行重启

配置Constrains限制该容器均从该主机重启

![img](http://note.youdao.com/src/2F69DDEB464C4F8EAFAC6B8D58C4D555)

/nexus-data/blobs/npm-repo-blob

/data/nexus/npm/npm-repo-blob