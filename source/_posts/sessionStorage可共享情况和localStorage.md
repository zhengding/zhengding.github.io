---
title: sessionStorage可共享情况和localStorage
date: 2019-04-19 14:23:13
tags:
- sessionStorage

---

1、不同浏览器无法共享localStorage和sessionStorage的值。

2、相同浏览器下，并且是同源窗口（协议、域名、端口一致），不同页面可以共享localStorage值，通过跳转的页面可以共享sessionStorage值。

3、关于sessionStorage，通常说sessionStorage关闭页面即消失，但是通过跳转的页面可以共享sessionStorage值，跳转有多种方式：

​    (1)  

```html
// 原窗口
<a href="同源页面" target="_self">跳转</a> 
```

​    (2)  

```html
// 新开窗口
<a href="同源页面" target="_blank">跳转</a> 
```

​    (3) window.location.href = '同源页面'      //原窗口

​    (4) window.location.replace('同源页面')   //原窗口

​    (5) window.open('同源页面')       //新开窗口

​    (6) this.$router.push({path: '同源页面'})   //通过路由跳转共享值

app端通过原生方法更换webView实现跳转，这种方式不能共享sessionStorage，原窗口跳转的页面传递sessionStorage，改变存储值会相互影响，新开窗口跳转方式传递sessionStorage，改变存储值互不影响。