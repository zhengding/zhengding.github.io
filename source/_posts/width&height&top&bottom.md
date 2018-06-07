---
title: width&height&top&bottom
data: 2018-5-25 10:07:01
tag:
- CSS
categories: 前端
---

1、**Element.getBoundingClientRect()**方法返回元素的大小及其相对于视口的位置。 

​	返回值是一个 [DOMRect](https://developer.mozilla.org/zh-CN/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIDOMClientRect) 对象，这个对象是由该元素的 [`getClientRects()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getClientRects) 方法返回的一组矩形的集合, 即：是与该元素相关的CSS 边框集合 。

​	DOMRect 对象包含了一组用于描述边框的只读属性——left、top、right和bottom，单位为像素。除了 width 和 height 外的属性都是相对于视口的左上角位置而言的。

```javascript
rectObject = object.getBoundingClientRect();
```

![getBoundingClientRectç¤ºä¾å¾](https://mdn.mozillademos.org/files/15087/rect.png) 