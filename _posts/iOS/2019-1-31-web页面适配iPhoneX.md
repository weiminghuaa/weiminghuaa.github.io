---
layout: post
title: web页面适配iPhoneX
date: 2019-01-31 15:30:35.000000000 +08:00
tags: ios
---

[iPhone X 适配 手Q H5页面通用解决方案](https://juejin.im/entry/5a03e173f265da43310d434f)
[Designing Websites for iPhone X](https://webkit.org/blog/7929/designing-websites-for-iphone-x/?hmsr=funteas.com&utm_medium=funteas.com&utm_source=funteas.com)
[网页适配 iPhoneX，就是这么简单](https://aotu.io/notes/2017/11/27/iphonex/index.html)

需要适配的点：顶部通栏的处理，底部通栏的处理，横屏的处理

适配方式：

1、旧页面，如果改造麻烦，可以先由客户端处理，要么客户端统一限制webview的显示区域，要么像手Q一样，跟据传进来的参数，做一些适配。强烈不推荐。

2、新页面，或者旧页面改造，就完全按照苹果给的浏览器规范，去设置```viewport-fit=cover```，以及跟据需要，去设置```safe-area-inset```，并不麻烦。