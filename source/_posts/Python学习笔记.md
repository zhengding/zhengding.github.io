---
title: Python学习笔记
data:  2018-3-12 14:39:01
tag:
- Python
categories: Python
---

### 1、计算表达式and、or：

python 中的 and 从左到右计算表达式，若所有值均为真，则返回最后一个值，若存在假，返回第一个假值；

or 也是从左到有计算表达式，返回第一个为真的值；

其中数字 0 是假，其他都是真；

字符 "" 是假，其他都是真；

### 2、二进制问题

```
a = 00111100
```

这么个赋值语句被提示了错误，于是去搜了下相关的博客得知 python 中数字有以下的表示方式：

**2** 进制是以 0b 开头的: 例如: 0b11 则表示十进制的 3

**8** 进制是以 0o 开头的: 例如: 0o11 则表示十进制的 9

**16** 进制是以 0x 开头的: 例如: 0x11 则表示十进制的 17

但是在测试的时候又遇到了个问题，那就是输出来的被自动转化成了十进制：

```
>>> a=0b111100
>>> a
60
```

于是又去找了怎么输出二进制，得到了以下内容：

分别使用 bin，oct，hex 可输出数字的二进制，八进制，十六进制形式，例如：

```
>>> a=0b111100
>>> a=60
>>> bin(a)
'0b111100'
>>> oct(a)
'0o74'
>>> hex(a)
'0x3c'
```