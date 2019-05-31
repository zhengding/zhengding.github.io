---
title: nexus 上搭建npm本地服务器-2
data:  2018-3-9 16:07:01
tag:
- npm
- nexus
categories: 前端
---

## 1.在本地运行nexus

附上下载链接：[Nexus Repository OSS 下载链接](https://link.jianshu.com?t=https://www.sonatype.com/download-oss-sonatype?hsCtaTracking=10655413-f621-4c62-be46-df84cf6b6b90%7C79f798b3-f0df-4370-b569-0eda6e14390e)
附上文档链接：[Nexus Repository OSS 文档链接](https://link.jianshu.com?t=https://help.sonatype.com/display/HSC/Node+Packaged+Modules+and+npm+Registries+-+NXRM+3)

> 有几个值得注意的是

- 我的 系统为 64位 win 7，平时只配置了java的基本环境，没有配置编译环境。很尴尬，配置javac耽误了我一点时间。需要的同学，在运行之前 需要配置一下jre环境。
- 我下载的是 nexus-3.5.0-02 这个版本。事实证明在win下虽然看起来一副人畜无害的样子，似乎可以直接点击安装，但是事实上，啧啧啧。

![img](http://upload-images.jianshu.io/upload_images/3112405-7d1345539585ca9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/438)

Paste_Image.png

这里没有好好阅读文档，也消耗了我的一些时间，下面放出win 下正确启动方法

```
// 在 \nexus-3.5.0-02\bin 目录下执行
$ nexus.exe /run

```

- 这里经测试 用win自带的cmd，和budun都可以比较好的启动，用git bash则启动不是很好

  ​

  ​

  ![img](http://upload-images.jianshu.io/upload_images/3112405-f69905866766485d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

  Paste_Image.png

启动之后默认地址为 localhost:8081, 默认账号密码  admin/ admin123

## Nexus Repository Manager 中添加 npm

如果点击：

![img](http://upload-images.jianshu.io/upload_images/3112405-6ed2f417bae14036.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

这里有三个类型的npm选项：

![img](http://upload-images.jianshu.io/upload_images/3112405-76bb9056ac0b4799.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

文档中对这三个类型的仓库有详细的解释：

![img](http://upload-images.jianshu.io/upload_images/3112405-5b66f23647b467bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

------

附上某歌翻译：

### Proxying npm Registries

为了减少开发人员和CI服务器的重复下载量并提高下载速度，您应该对托管在[https://registry.npmjs.org](https://link.jianshu.com?t=https://registry.npmjs.org)上的注册表进行代理。 默认情况下，npm直接访问此注册表。 您还可以代理您需要的任何其他注册表。
要代理外部npm注册表，您只需创建一个新的npm（代理），如“存储库管理”中所述。
最小配置步骤是：

- 定义名称
- 定义远程存储的URL，例如[https://registry.npmjs.org](https://link.jianshu.com?t=https://registry.npmjs.org)
- 选择存储的Blob存储

### Private npm Registries

私人npm注册表可用于上传您自己的软件包以及第三方软件包。 您可以通过在存储库管理器中设置npm格式的托管存储库来创建私有的npm注册表。 为了这些目的，建立两个独立的托管存储库是一个很好的做法。
要创建具有npm格式的托管存储库，只需创建一个新的npm（托管），如“存储库管理”中所述。
最小配置步骤是：

- 定义名称
- 选择存储的Blob存储

随着软件包从存储库部署或删除，npm注册表信息将立即更新。

### Grouping npm Registries

存储库组是将所有npm注册表存储库从存储库管理器公开给您的用户的推荐方式，而不需要任何其他客户端配置。存储库组允许您将多个代理和托管存储库的聚合内容公开为一个URL到npm和其他工具。这可以通过创建一个新的npm（组），如“存储库管理”中所述，用于npm存储库。
最小配置步骤是：

- 定义名称
- 选择存储的Blob存储
- 以所需的顺序将npm存储库添加到成员列表中

典型的有用的示例是将代理存储库分组：代理npm注册表，npm托管存储库与内部软件包和另一个npm托管存储库与第三方软件包。
使用存储库组的URL作为客户端工具中的npm存储库URL，您可以使用一个URL访问所有三个存储库中的软件包。添加的任何新软件包以及添加到组中的任何新存储库将自动可用。

------

1. 创建 Proxying npm Registries 代理仓库

![img](http://upload-images.jianshu.io/upload_images/3112405-37ebbc0d64c830a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

1. 创建 Hosted npm Registries 代理仓库

![img](http://upload-images.jianshu.io/upload_images/3112405-66c19792d1a1af8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

1. 创建  Grouping npm Registries 仓库
   需要注意的是组仓库要把之前的 proxy 和 hosted 都添加上。

![img](http://upload-images.jianshu.io/upload_images/3112405-4a3b73e82ef008b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

> 我的目录对应为  mytest/myhost/mygroup

在home目录下， 的.npmrc文件中添加 registry = [http://localhost:8081/repository/npm-all/](https://link.jianshu.com?t=http://localhost:8081/repository/npm-all/) 。这个路径要和自己的路径一致，并且最后的rep名要和group相同。

win下 home路径：

![img](http://upload-images.jianshu.io/upload_images/3112405-3d22c1473c8540f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/456)

Paste_Image.png

这时尝试一下

```
// 查看一下 是否从本地服务器中下载
$ npm -loglevel info install grunt

```

![img](http://upload-images.jianshu.io/upload_images/3112405-24fa52ac1a45328a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

图片我抄的，我自己的没截图

此时可以验证一下 是否依赖走了代理：

![img](http://upload-images.jianshu.io/upload_images/3112405-f8a82f22a035ae41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/558)

Paste_Image.png

## 设置权限

把这个选项拖到右边就可以了

![img](http://upload-images.jianshu.io/upload_images/3112405-d90bcf8c8fdcdb2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

在admin之外可以重新设置一个账号，以及权限

![img](http://upload-images.jianshu.io/upload_images/3112405-3a59e0300667d076.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

```
// 使用
$ npm login –registry=http://localhost:8081/repository/mygroup
// 进行登陆，需要填写账号、密码以及邮箱。

```

![img](http://upload-images.jianshu.io/upload_images/3112405-65b42a283b08a7cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/696)

Paste_Image.png

## 发布到hosted

> 我和原教程不一样的是：我发布到本地hosted， 而不是直接到npm社区，这里需要注意的是 通过proxy是可以直接发布到npm社区的，使用的是社区的账号密码，这个需要提前申请

> 要发布的模块，必须保证在根目录下有package.json文件，否则会报错。

pachage.json文件内容比较随意，如果比较懒 可以用  npm init 直接自动生成。

```
  {
      "name": "myhost",
      "version": "1.0.0"
    }

```

然后执行发布命令：

```
 $ npm publish –registry http://localhost:8081/repository/myhost

```

![img](http://upload-images.jianshu.io/upload_images/3112405-7ff83834a03ba6c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/659)

Paste_Image.png

然后我遇到了报错，按照报错执行

```
 $ npm adduser –registry http://localhost:8081/repository/myhost

```

> 这里值得注意的是， adduser的时候要指明是哪个rep，要不就会add到默认的.npmrc 里添加的 rep中

![img](http://upload-images.jianshu.io/upload_images/3112405-b9cd8079d4a9c1be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/685)

Paste_Image.png

添加完用户之后，再执行发布命令就ok了。

![img](http://upload-images.jianshu.io/upload_images/3112405-394b1c8bce17b24a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/674)

Paste_Image.png

package.json可以指定name 和version并且可以添加发布路径的配置：

```
  {
     "name": "intro",
     "version": "1.0.0",
     "publishConfig" : {
         "registry" : "http://localhost:8081/repository/myhost/"
      }
  }

```

这样的话发布直接用 npm publish就可以了。

发布之后可以在 系统中的 左侧browse 中的assets中看到上传的资源

![img](http://upload-images.jianshu.io/upload_images/3112405-70f6dd148c39055d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

Paste_Image.png

group 中可以同时看到 proxy 和 hosted 的资源， proxy会存下第一次下载过的目录，之后再下载时走的是代理，hosted存的是本地上传的包资源。

然后可以在本地下载一下刚才上传到hosted的包：

![img](http://upload-images.jianshu.io/upload_images/3112405-23f5a0982a8abcc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/630)

Paste_Image.png

以上，为所有内容。