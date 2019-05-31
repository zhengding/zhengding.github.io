---
title: 在Deepin15.10系统中去掉主板蜂鸣器提示警告声音的方法
date: 2019-05-31 16:22:51
tag:
- deepin
categories: 运维
---

全新安装深度Deepin 15.10操作系统后，在命令行中遇到提示警告声音就是来自于主板的蜂鸣器，那个声音非常的刺耳，这个并不是控制中心的音效功能。其实这个是Bash终端自带的功能，有些Bash就是这样，比如xterm就是，并非Deepin 15.10版本才有（参考：[深度操作系统Deepin 15.10下载地址，附新功能介绍](https://ywnz.com/linuxxz/4897.html)），其它的Deepin版本并没有这个提示声音。我们可以使用下面的解决方法去掉这个声音。

 

**解决方法**

1、去掉命令行中Tab键的提示音，运行如下命令：

$ echo set bell-style none >> ~/.inputrc

2、去除Vim中的Tab键和Backspace键的提示音，运行如下命令：

$ echo xset -b >> ~/.bashrc

参考：[什么是bashrc配置文件，如何对其进行编辑](https://ywnz.com/linuxjc/1011.html)。

注：经过以上两个步骤的操作，你的Deepin 15.10命令行就恢复平静了。