---
title: react引入antd
data:  2017-12-21 18:39:01
tag:
-  react
- antd
categories: 前端
---

需安装babel-plugin-import插件

```javascript
{
  test: /\.(js|jsx)$/,
  include: paths.appSrc,
  loader: require.resolve('babel-loader'),
  options: {
  plugins: [
  ['import', [{ libraryName: 'antd', style: true }]],  // import less
  'react-hot-loader/babel'
  ]
},
```

