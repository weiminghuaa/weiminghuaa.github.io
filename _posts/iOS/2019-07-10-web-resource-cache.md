---
layout: post
title: web资源缓存
date: 2019-07-10 15:30:35.000000000 +08:00
tags: ios
---

总是没搞清楚web的缓存机制，所以对web加载优化总是云里雾里。这次一次性搞清楚，环境分为客户端的webview（以ios的wkwebview为例）和客户端外的浏览器（以pc端的chrome为例），以设置了Expires的web资源为测试对象

ps：过期时间外的缓存请求是304，正常。过期时间内，要验证不同场景下是否会重复发送请求

## 同一个页面的资源缓存

A.html -> a.css

- chrome: 

  - 刷新，新开窗口，重启chrome，都不会发送已缓存的资源请求，直接使用缓存

- wkwebview：

  - 刷新，重新打开页面，重启APP，都不会发送已缓存的资源请求，直接使用缓存

## 不同同页面的资源缓存

1. 路径相同的2个html页面，共享资源

A.html -> a.css
B.html -> a.css

  - chrome: 

    - 刷新，重新打开页面，重启APP，都不会发送已缓存的资源请求，直接使用缓存，即共享a.css

  - wkwebview：

    - 刷新，重新打开页面，重启APP，都不会发送已缓存的资源请求，直接使用缓存，即共享a.css


2. 路径不同的页面，不共享资源

## 一篇文章

iOS APP秒开H5实战总结：
http://mp.weixin.qq.com/s?__biz=MzA5NzMwODI0MA==&mid=2647763968&idx=1&sn=a2e890cb4cc5010d1dd8d563cc283491&chksm=8887d73fbff05e2972020ba82a7f0cdfa0cf85a19549a818e2a7a2c0dc7e583bd447eb09ad3f&mpshare=1&scene=23&srcid=#rd
