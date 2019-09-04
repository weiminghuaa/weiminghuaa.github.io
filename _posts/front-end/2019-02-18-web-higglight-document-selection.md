---
layout: post
title:  web higglight document selection
date: 2019-02-18 21:58:35.000000000 +08:00
tags: [前端, chrome, extension]
---

需要做一个高亮显示网页中选中的文本的效果，将预研过程列一下

## 高亮绘制实现

### jquery

![高亮documen的selection_jquery](/assets/htmls/高亮documen的selection_jquery.html)

查找所有的匹配文字并高亮，虽不符合需求，但是网上只有这个，最开始从这入手

### stockoverflow

![高亮documen的selection_stockoverflow](/assets/htmls/高亮documen的selection_stockoverflow.html)

后来想了一下，还是得将重点拆开

1、selection是dom的api，去mdn看了相关的介绍，知道anchorNode和focusNode，期间补了一下node的知识

2、没有api提供selection的所有node，stockoverflow有人写了这个

3、获取了所有的node，其中标签之间的换行，完全没用需要过滤，参考diigo用```n.nodeValue.replace(/(^\s+|\s+$)/mg, "").length > 0```判断

### diigo

![高亮documen的selection_diigo](/assets/htmls/高亮documen的selection_diigo.html)

diigolet.js 用另一种思路实现了，是最好的方案，虽然diigolet.js的代码混淆了，还是一步步还原了出来

## 数据存储等

### note anywhere

本地的 H5 database

### diigo

远程的 web api, 数据结构：

```json
{
	"code": 1,
	"user": "weiminghuaa",
	"result": {
		"pageComments": [],
		"url": "http://wereadteam.github.io/2016/02/22/MLeaksFinder/",
		"bookmarkInfo": {
			"mode": 2,
			"outliners": [],
			"datetime": "on 2019-02-02",
			"description": "",
			"unread": true,
			"title": "MLeaksFinder：精准 iOS 内存泄露检测工具 | WeRead团队博客",
			"lists": [],
			"tags": []
		},
		"b_id": "ec8fdb80843e35cdf2be1e78536bcd51",
		"urlId": "c3cacecaf8b25fae171e7b601c46a868",
		"cached": 0,
		"annotations": [{
			"user": "weiminghuaa",
			"ann_id": 143061545,
			"type": 0,
			"onlyInGroup": false,
			"mode": 2,
			"content": "Memory",
			"extra": {
				"top": 554,
				"nth": 2,
				"left": 212.796875,
				"color": "yellow"
			},
			"comments": [],
			"realName": " ",
			"id": "66d95e1259c907f9c819439e1b763b9c",
			"userOnline": false,
			"groups": []
		}, {
			"user": "weiminghuaa",
			"ann_id": 138686799,
			"type": 0,
			"onlyInGroup": false,
			"mode": 2,
			"content": "在 MRC 时代 Leaked memory 很常见，因为很容易忘了调用 release，但在 ARC 时代更常见的内存泄露是循环引用导致的 Abandoned memory，Leaks 工具查不出这类内存泄露，应用有限。",
			"extra": {
				"top": 726.328125,
				"nth": 1,
				"left": 317,
				"color": "yellow"
			},
			"comments": [],
			"realName": " ",
			"id": "c3efc21f98e500a2a761cf3546c62baa",
			"userOnline": false,
			"groups": []
		}],
		"saved": true,
		"annotated": true,
		"groups": []
	},
	"cmd": "bm_loadBookmark",
	"transId": "25"
}
```

## 高亮节点查找

获取高亮数据后，怎么查到高亮节点，以进行绘制呢？

### diigo

API返回的数据中，有content，left，top，其中content就是高亮的文本内容，left和top是坐标，nth是出现在第几次，diigo是根据nth来查找高亮节点的。

content:

<p>tions 会记录从上回内存快照到这次内存快照这个时间段内，新分配的内存信息。举一个最简单的例子：</p>\n<p>我们可以不断重复 push 和 pop 同一个 UIViewController，理论上来说，push 之前跟 pop 之后，app 会回到相同的状态。因此，在 push 过程中新分配的内存，在 pop 之后应该被 dealloc 掉，除了前几次 push 可能有预热数据和 cache 数据的情况。如果在数次 push 跟 pop 之后，内存还不断增长，则有内存泄露。因此，我们在每回 push 之前跟 pop 之后，都 Mark Generation 一下，以此观察内存是不是无限制增长。这个方法在 WWDC 的视频里：<a href=\"http://developer.apple.com/videos/wwdc/2010/\" target=\"_blank\" rel=\"external\">Session 311 - Advanced Memory Analysis with Instruments</a>，以及苹果的开</p>

> 为什么不直接保存node节点呢？

因为text节点就只有内容，如果要直接保存节点，那就要保存节点在document中的的位置等数据，好像也不是不可以，但是如果document有变动，那就找不到了。


> 实现

nth如何计算(4656行)：计算从最开始，到当前选中，一共多少次，nth即最大那一次。

如何根据nth找node节点：seek方法

docTxtOffsetList：这是预先生成的，整个html的text节点数组，数据结构为array，元素为：{length:4, node: text, offset: 5890}

seek方法内(4401行)，for 循环遍历 docTxtOffsetList，得到需要绘制的节点：

```
{
  startNode: m,
  endNode: t,
  startOffset: C,
  endOffset: A,
  endIndex: h,
  commonAncestor: G,
  txt: d
}
```