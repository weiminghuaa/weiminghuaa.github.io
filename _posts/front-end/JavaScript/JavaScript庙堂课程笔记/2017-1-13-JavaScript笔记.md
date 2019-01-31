---
layout: post
title: JS学习
date: 2017-1-13 21:58:35.000000000 +08:00
---

# JavaScript笔记-20170113

## JavaScript Window - 浏览器对象模型

浏览器对象模型 (BOM) 使 JavaScript 有能力与浏览器“对话”。浏览器对象模型（Browser Object Model）尚无正式标准。

###Window 对象

所有浏览器都支持 window 对象。它表示浏览器窗口。
所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。
全局变量是 window 对象的属性。
全局函数是 window 对象的方法。
甚至 HTML DOM 的 document 也是 window 对象的属性之一：
```javasrcipt
window.document.getElementById("header");
与此相同：
document.getElementById("header");
```

## JavaScript prototype 属性

prototype 属性使您有能力向对象添加属性和方法。
语法
object.prototype.name=value

```javasrcipt
<script type="text/javascript">

function employee(name,job,born) {
    this.name=name;
    this.job=job;
    this.born=born;
}

var bill=new employee("Bill Gates","Engineer",1985);

employee.prototype.salary=null;
bill.salary=20000;

document.write(bill.salary);

</script>
```