---
layout: post
title: bxr_events学习
date: 2018-11-26 21:58:35.000000000 +08:00
tags: [Learn, 前端, Vue]
---

bxr_events是公司的代码库，存放了所有活动页面的前端代码

## m20180710BlackGold-wmh

copy自m20180710BlackGold2，同时做一些改造，希望放弃jquery，使用感觉更为现代化的库实现

### 页面结构

```html
<!-- 最外层 -->
<div id="vm" v-cloak> 
    <!-- 背景，包含随背景滚动的奖励提示 -->
    <div class="bg">
      <div :class="isJoin?'top2':'top1'">
        <p v-show = "isJoin"><span>{{aniNumber}}</span></p>
      </div>
    </div>
    <!-- 按钮，不随背景滚动，独立于背景 -->
    <div class="click" data_href="imbxr://zdb/main">
      <p>{{clickText}}</p>
    </div>
    <!-- 参加活动后的弹窗，独立于背景 -->
    <div class="pop" v-show="popShow" @click="popShow = false">
      <div class="pop_content">
        <div class="pop_btn"></div>
      </div>
    </div>
  </div>
```

### 知识点

1. v-cloak: 解决 Vue 在渲染元素出现闪现的问题

2. mock: 在m20180905Ticket项目中，使用类似debug，release等标识来区分是否使用mock数据

3. 调试: 开发中，通过mock来做调试，没有单元测试；流程上的测试，通过gulp把项目发布到测试环境，进行测试；最后上线

4. 开发模式: 项目中现有的开发，不好调试，方法定义的跳转都不支持。vue-cli，很不错，支持这些，我尝试看看能不能通过vue-cli重构项目

### vue-cli

问题：

1. 如何像 fis3 一样在 head 中嵌入资源