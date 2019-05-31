---
title: 解决mac（windows）终端上代理的难题
data:  2017-12-12 14:07:01
tag:
-  ShadowSocks
categories: 干货
---

买了 ss （shadowsocks）帐号，装了 ss 代理的软件，浏览器上也装了代理的插件，一直用的都挺好的，但是最近遇到了一些情况
情况1：最近网络抽风，网页上访问 github 还好，但是在终端里，git clone 一个 repository，或者在 webstorm 中检出一个 github 上的repository，那是相当的痛苦
情况2：同事在安装 brew 的过程中，需要下载 github 上的repository，于是遭受了和上面一样的痛苦
不仅仅如此，为了解决类似的麻烦，很多人使用了 cnpm（淘宝出的代替 npm 的工具），或者更换各种包仓库的地址为国内镜像的地址，归根到底为一个原因，终端里的命令：诸如 wget，curl，git ，npm 等等，不好设置代理翻墙。
经过本人的探索，找出了一条解决之道，先不说麻不麻烦，总归是解决了长久以来困扰自己的问题
你所需要的，要有一个 ss 帐号，mac 上安装 shadowsocksX，已设置本地代理：socks5：127.0.0.1：1080
还需要另外一个神器：proxifier
举个例子，在终端里 git clone 一个repository，究竟是什么程序发起了网络请求，我现在可以直接告诉你答案，是 git-remote-https，请求 github.com:443，使用proxifier，就可以做到这一点，定位到是什么程序，请求的 host 以及 port，并且给它设置代理
下面这张图就是proxifier的庐山真面目了
![image_1b4oengilkbc1djbb48m7e1hbnm.png-370.8kB](../images/解决mac(windows)终端上代理的难题/image_1b4oengilkbc1djbb48m7e1hbnm.png)
上面的日志给出了详细的解释   
当我在终端里敲下 npm install xxx，对应的就是 node 请求 registry.npmjs.org:443   
当我在webstorm中设置 github 帐户的时候，对应的是 webstorm，请求 api.github.com:443   
当我在 git clone repository的时候，对应的是git-remote-https 请求 github.com:443
第一步：你得在shadowsocksX中开启 socks5 的本地代理，这是最重要的前提   
第二步：在proxifier使用第一步设置的代理，如下图所示
![image_1b4ofiiia5821397ea0phm6rn13.png-261.8kB](../images/解决mac(windows)终端上代理的难题/image_1b4ofiiia5821397ea0phm6rn13.png)
第三步：设置代理规则   
![image_1b4og01u91alg1ggk1mh51omdurr1g.png-479.7kB](../images/解决mac(windows)终端上代理的难题/image_1b4og01u91alg1ggk1mh51omdurr1g.png)
解释一下我所设置的规则：   
![image_1b4og01u91alg1ggk1mh51omdurr1g.png-479.7kB](../images/解决mac(windows)终端上代理的难题/image_1b4og01u91alg1ggk1mh51omdurr1g-1554097364886.png)
规则匹配的顺序是从上往下的   

1. 生效的第一条规则是shadowsocksX，因为我们的代理就是靠这个程序的，所以不能让它自己代理自己，否则proxifier 会报无限循环，动作选 direct   
2. 规则 github，这里也并没有指定程序git-remote-https，因为 像 webstorm等 GUI 之流，也会访问 github：443的请求，所以不指定程序，只限制 主机和端口，应用范围就不限于终端了   
3. 规则 node，解决 npm install 的代理，可以不用 cnpm 了   
4. 规则 brew，解决终端下使用 brew 安装 package 的代理   
5. 规则 google-analytics，，解决终端下使用 brew 安装 package 的代理   
6. 规则 default，不能删除，当匹配不到任何规则的时候，使用该规则
任何一个连接请求，从上往下匹配，匹配到了，就会停止   
以上就是我摸索出来的方法，如果 ss 速度快的话，相信我，你会开心死的
一言以蔽之，如果你在终端中发起了网络请求速度很慢的话，可以先把 default 规则，设置为socks5代理（只有设置为代理，日志才会记录），然后分析一下请求的 host：port，然后就可以添加规则了，这样的话，就做到只给部分请求走代理，如果你觉得这样很麻烦，   
也可以简单粗暴的，将 default 规则设置为 socks5代理，其余的规则全部禁用，那就是全部的请求走代理
ps：shadowsocks和proxifier windows 下也有，按照道理，windows 下也可用，我自己未测试过



[image-1]:	http://p09eeagrw.bkt.clouddn.com/image_1b4oengilkbc1djbb48m7e1hbnm.png
[image-2]:	http://p09eeagrw.bkt.clouddn.com/image_1b4ofiiia5821397ea0phm6rn13.png
[image-3]:	http://p09eeagrw.bkt.clouddn.com/image_1b4og01u91alg1ggk1mh51omdurr1g.png
[image-4]:	http://p09eeagrw.bkt.clouddn.com/image_1b4og4c2k2i6j8b1jdjd03has1t.png