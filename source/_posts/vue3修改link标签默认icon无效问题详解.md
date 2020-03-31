---
title: vue3修改link标签默认icon无效问题详解
date: 2019-11-26 09:23:48
tags:
- vue
- icon
categories: vue
---

 在vue.config.js中配置下如下代码，重跑下项目就行了

```javascript
// 以下是pwa配置
pwa: {
  iconPaths: {
    favicon32     : 'faviconfc.ico',
    favicon16     : 'faviconfc.ico',
    appleTouchIcon: 'faviconfc.ico',
    maskIcon      : 'faviconfc.ico',
    msTileImage   : 'faviconfc.ico'
  }
},
```
