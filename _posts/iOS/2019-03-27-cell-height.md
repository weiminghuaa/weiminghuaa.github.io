---
layout: post
title: cell高度问题
date: 2019-03-27 15:30:35.000000000 +08:00
tags: ios, learn
---

这都9102年了，我还在手动计算cell高度，今天终于把这个问题搞定了。写了个demo：TestTableVIewEstimatedRowHeight

手动计算的繁琐，简直是反人类的设计。没有使用自动计算，主要是因为view隐藏时，间距的问题，看看这个：https://stackoverflow.com/questions/18065938/how-to-use-auto-layout-to-move-other-views-when-a-view-is-hidden

里面有很多方案，最省事的是用 UIStackView，但是逐个设置间距需要iOS11才支持；另一方案是手动修改间距。我都实现了一遍，参考demo。

这个问题折腾好久了，说明自己的两个问题：1、研究能力不强，探索能力不强，应该更大胆一点；2、懒，所以很多事情烂尾了，应该勤奋一点，至少不能拖个几年；3、英文水平太重要，有时候想研究，搜不到资料，还是要好好学英文的。