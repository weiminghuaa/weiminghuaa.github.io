# JavaScript笔记--2017-02-04.md

## rel

```
<link href="miaov_style.css" rel="stylesheet" type="text/css" />
<!-- 
rel 属性规定当前文档与被链接文档之间的关系。
浏览器支持
只有 rel 属性的 "stylesheet" 值得到了所有浏览器的支持。其他值只得到了部分地支持。 
rel="stylesheet" 属性指示被链接的文档是一个样式表
-->
```

## longdesc

```
<div class="url" title="妙味课堂-超酷时钟"><a href="http://www.miaov.com/"><img src="images/miaov.png" alt="妙味课堂" longdesc="http://www.miaov.com" /></a></div>
</div>
<!--
定义和用法
longdesc 属性规定指向图像描述信息页面的 URL。
详细信息
<img> 标签的 longdesc 属性与 alt 属性类似，但它允许更长的描述性文字。longdesc 的值是一个指向包含图像说明的文档的 URL。如果说明多于 1024 个字符，那么可以使用 longdesc 属性来设置指向它的链接。
不论是 HTML 还是 XHTML 都没有指定说明中必须是什么内容，而且目前浏览器都还没有支持 longdesc 属性。因此，对于如何创建那些大块的描述，我们的建议是创建一个超链接指向包含描述的页面即可。
alt 属性对于残疾人很有帮助，而出于显而易见的原因考虑，longdesc 属性似乎是更好的选项。
浏览器支持
没有浏览器支持 longdesc 属性。 -->
```

## 当前时间及处理

```
	var oDate=new Date();
	
	var iYear=oDate.getYear();
	var iMonth=oDate.getMonth();
	var iDay=oDate.getDate();
	var iHour=oDate.getHours();
	var iMin=oDate.getMinutes();
	var iSec=oDate.getSeconds();
	var iWeek=(oDate.getDay()+6)%7;
```

## 定时器-setTimeout

延迟执行

## 定时器-setInterval

重复调用一个函数或执行一个代码段，以固定的时间延迟在每次调用之间。返回一个 intervalID。

###语法
```
var intervalID = window.setInterval(func, delay[, param1, param2, ...]);
var intervalID = window.setInterval(code, delay);
```

###参数
	•	intervalID 是此重复操作的唯一辨识符，可以作为参数传给clearInterval()。
	•	func 是你想要重复调用的函数。
	•	code 是另一种语法的应用，是指你想要重复执行的一段字符串构成的代码(使用该语法是不推荐的，不推荐的原因和eval()一样)。
	•	delay 是每次延迟的毫秒数 (一秒等于1000毫秒)，函数的每次调用会在该延迟之后发生。和setTimeout一样，实际的延迟时间可能会稍长一点
		
### [例子](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setInterval)
```
g_oTimer=setInterval(doMove, 30); // 每30毫秒调用doMove;
function stopMove()
{
	clearInterval(g_oTimer);
	g_oTimer=null;
}
```

## 定时器与动画

[超酷数码时钟](file:///Users/weiminghua/Documents/JavaScript/part1/0/课程案例3/超酷数码钟表/miaov_demo.html)

[图片循环滚动](file:///Users/weiminghua/Documents/JavaScript/part1/0/课程案例3/无缝滚动图片/miaov_demo.html)

[QQ延时提示框](file:///Users/weiminghua/Documents/JavaScript/part1/0/课程案例4/QQ延时提示框/qq.html)

等等案例

## href="#"与href="javascript:void(0)"的区别

- "#"包含了一个位置信息，默认的锚是#top 也就是网页的上端。在页面很长的时候会使用 # 来定位页面的具体位置，格式为：# + id。
- 而javascript:void(0), 仅仅表示一个死链接。点击不做任何事情


