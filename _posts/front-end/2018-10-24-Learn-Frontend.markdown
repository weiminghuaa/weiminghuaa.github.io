---
layout: post
title: 前端技术学习
date: 2018-10-24 21:58:35.000000000 +08:00
tags: [Learn, 前端]
---

前端的学习一直是心病，js各种资料和教程学习N遍，但是又啥也不懂，因为没有工程实践，万事开头难。所以应该是工程入手，暂时放弃书本式学习的方法。学习和实战的路径应该是：
1、通过公司项目，大概了解vue
2、通过小程序项目，掌握vue（就像swift和rxswift一样，用的多了，就差不多会了）
3、再慢慢了解其他诸如打包构建之类的东西（就像开始iOS，对xcode的project setting一头雾水一样，用的多了就熟悉了）


## 技术资料和视频的学习

[MDN](https://developer.mozilla.org/zh-CN/)

比较全的技术文档

### JS

1. [慕课网-JavaScript](http://www.imooc.com/course/list?c=javascript)

学完了【JavaScript进阶篇】、【JavaScript进阶篇】，比较基础，体验很好，有很多课程，以后有针对性的学习。

2. [宁浩网-JavaScript](https://ninghao.net/package/javascript)

钟雨成推荐的，还没去看，都是视频，没时间啊

3. 书《JavaScript-权威指南》

我应该看了大半

4. 其他视频

看了一些，不完整，太费时间

### CSS

[MDN-CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS)

看了大半了

### 其他

[JavaScript 教程](https://wangdoc.com/javascript/index.html)  更多从实践的角度写的，应该可以挑着看看
[ES6 标准入门](http://es6.ruanyifeng.com/#README) 看了又忘的东西，先不看了
[深入浅出 Webpack](http://webpack.wuhaolin.cn) 先瞄一瞄


## 技术实践

### 向日葵前端项目

1. 开发

chrome调试，问题是，代码编辑器不能实现：点击不能跳转到方法定义，跟Android、iOS的IDE很不一样。

前端技术上东西太多，提说重复的轮子很多，感觉很复杂；但是有些又很简单，比如`bxr_event`，经常整页都是图片，加少量交互实现。

2. CI&CD

使用fis3构建，

jenkins打包？

发布
`bxr_event`看到最新的做法，是使用`gulpfile`执行`task`，完成自动发布，发布使用的是`ftp`

4. 主要用了些什么技术？

> jquery

这个谁都会

> fis3

项目构建，强大，具体怎么配置的，一时也不清楚，后面用到再查

> Vue

`bxr_event`最新的一些活动，就开始使用`Vue`了；更大的工程，比如`商城`，应该是用`Vue`

> 自己的build-framework

前端自己做了一套framework，封装了一些功能，主要是layout-1.0.0.js

> gulpfile

用于自动发布

> vConsole

腾讯的，类似iOS的FLEX (Flipboard Explorer)

> 其他...

前端的东西太多了

### 微信小程序

