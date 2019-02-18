---
layout: post
title:  web higglight document selection
date: 2019-02-18 21:58:35.000000000 +08:00
tags: [前端, chrome, extension]
---

需要做一个高亮显示网页中选中的文本的效果，将预研过程列一下

## jquery

![](/assets/htmls/高亮documen的selection_jquery.html)

查找所有的匹配文字并高亮，虽不符合需求，但是网上只有这个，最开始从这入手

## stockoverflow

![](/assets/htmls/高亮documen的selection_stockoverflow.html)

后来想了一下，还是得将重点拆开

1、selection是dom的api，去mdn看了相关的介绍，知道anchorNode和focusNode，期间补了一下node的知识

2、没有api提供selection的所有node，stockoverflow有人写了这个

3、获取了所有的node，其中标签之间的换行，完全没用需要过滤，参考diigo用```n.nodeValue.replace(/(^\s+|\s+$)/mg, "").length > 0```判断

## diigo

![](/assets/htmls/高亮documen的selection_diigo.html)

diigolet.js 用另一种思路实现了，是最好的方案，虽然diigolet.js的代码混淆了，还是一步步还原了出来