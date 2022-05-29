# JavaScript笔记--2017-02-05.md

[TOC]

## JS调试

javascript调试最常用的是全局方法console.log()，可以用它输出想要跟踪查看的变量，或者单纯地输出一些字符串说明调试语句所在的代码段有没有被执行到。

浏览器的开发者工具，在控制台中可以查看到当前页面的输出信息（通过console.log()），以及javascript错误。错误信息还会说明错误位置（文件名，行，列），错误类别，错误说明。

## JavaScript 实现

尽管 ECMAScript 是一个重要的标准，但它并不是 JavaScript 唯一的部分，当然，也不是唯一被标准化的部分。实际上，一个完整的 JavaScript 实现是由以下 3 个不同部分组成的：
	•	核心（ECMAScript）
	•	文档对象模型（DOM）
	•	浏览器对象模型（BOM)
	
- JavaScript 的核心 ECMAScript 描述了该语言的语法和基本对象；
- DOM 描述了处理网页内容的方法和接口；
- BOM 描述了与浏览器进行交互的方法和接口。



## JS组成中的：ECMAScript简介

ECMAScript是一种由Ecma国际（前身为欧洲计算机制造商协会）通过ECMA-262标准化的脚本程序设计语言。这种语言在万维网上应用广泛，它往往被称为JavaScript或JScript，但实际上后两者是ECMA-262标准的实现和扩展。

1995年12月，升阳公司与网景公司公司一起引入了JavaScript[1]。1996年3月，网景公司发表了支持JavaScript的网景导航者 2.0。由于JavaScript作为网页的客户端脚本语言非常成功，微软于1996年8月引入了Internet Explorer 3.0，这个软件支持一个“约”与JavaScript相容的JScript[2]。
1996年11月，网景公司将JavaScript提交给欧洲计算机制造商协会进行标准化。ECMA-262的第一个版本于1997年6月被Ecma组织采纳[3]。
ECMAScript是由ECMA-262标准化的脚本语言的名称。JavaScript和JScript与ECMAScript相容，但包含超出ECMAScript的功能。

### 版本

至今为止有七个ECMA-262版本发表。

版本 | 发表日期 | 与前版本的差异
--------- | ------------- | -------------
1 | 1997年6月 | 首版
2 | 1998年6月 | 格式修正，以使得其形式与ISO/IEC16262国际标准一致
3 | 1999年12月 | 强大的正则表达式，更好的词法作用域链处理，新的控制指令，异常处理，错误定义更加明确，数据输出的格式化及其它改变
4 | 放弃 | 由于关于语言的复杂性出现分歧,第4版本被放弃,其中的部分成为了第5版本及Harmony的基础。
5 | 2009年12月[4] | 新增“严格模式（strict mode）”，一个子集用作提供更彻底的错误检查,以避免结构出错。澄清了许多第3版本的模糊规范,and accommodates behaviour of real-world implementations that differed consistently from that specification。增加了部分新功能,如getters及setters,支持JSON以及在物件属性上更完整的反射。[5][6][7][8]
6 | 2015年6月 | 多个新的概念和语言特性。ECMAScript Harmony将会以“ECMAScript 6”发布。
7 | 2016年6月[9] | 多个新的概念和语言特性


2004年6月Ecma组织发表了ECMA-357标准，它是ECMAScript的一个扩延，也被称为E4X（ECMAScript for XML）。

## JS组成中的：DOM简介

- 文档对象模型（英语：Document Object Model，缩写DOM），是W3C组织推荐的处理可扩展置标语言的标准编程接口。
- Document Object Model的历史可以追溯至1990年代后期微软与Netscape的“浏览器大战”（browser wars），双方为了在JavaScript与JScript一决生死，于是大规模的赋予浏览器强大的功能。微软在网页技术上加入了不少专属事物，既有VBScript、ActiveX、以及微软自家的DHTML格式等，使不少网页使用非微软平台及浏览器无法正常显示。DOM即是当时蕴酿出来的杰作。
- DOM（文档对象模型）是 HTML 和 XML 的应用程序接口（API）。DOM 将把整个页面规划成由节点层级构成的文档。HTML 或 XML 页面的每个部分都是一个节点的衍生物。

### 标准化

W3C对DOM进行标准化的动作，目前已经推行至第三代。

## JS组成中的：BOM简介

IE 3.0 和 Netscape Navigator 3.0 提供了一种特性 - BOM（浏览器对象模型），可以对浏览器窗口进行访问和操作。使用 BOM，开发者可以移动窗口、改变状态栏中的文本以及执行其他与页面内容不直接相关的动作。

- 浏览器对象模型 (BOM) 使 JavaScript 有能力与浏览器“对话”。
- 浏览器对象模型（Browser Object Model）尚无正式标准。
- 由于现代浏览器已经（几乎）实现了 JavaScript 交互性方面的相同方法和属性，因此常被认为是 BOM 的方法和属性。
- 由于没有相关的 BOM 标准，每种浏览器都有自己的 BOM 实现。有一些事实上的标准，如具有一个窗口对象和一个导航对象，不过每种浏览器可以为这些对象或其他对象定义自己的属性和方法。

### Window 对象

所有浏览器都支持 window 对象。它表示浏览器窗口。
所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。
全局变量是 window 对象的属性。
全局函数是 window 对象的方法。
甚至 HTML DOM 的 document 也是 window 对象的属性之一：
window.document.getElementById("header");

## ECMAScript、DOM、BOM三者的浏览器兼容性问题

- ECMA 几乎没有兼容性问题
- DOM 有一些操作不兼容
- BOM 没有兼容问题（完全不兼容）

## 变量类型——typeof检测类型

类型：typeof运算符

- 用法、返回值
- 常见类型：number、string、boolean、undefined、object、function
- 一个变量应该只存放一种类型的数据

- 显式类型转换(强制类型转换)
	*	parseInt()、 parseFloat()
	*	NaN的意义和检测
- 隐式类型转换
	* 相等==，全等===
	* 减法

## parseInt函数介绍

```
parseInt("10");			//返回 10
parseInt("19",10);		//返回 19 (10+9)
parseInt("11",2);		//返回 3 (2+1)
parseInt("17",8);		//返回 15 (8+7)
parseInt("1f",16);		//返回 31 (16+15)
parseInt("010");		//未定：返回 10 或 8
```

## JavaScript NaN 属性

###定义和用法

NaN 属性是代表非数字值的特殊值。该属性用于指示某个值不是数字。可以把 Number 对象设置为该值，来指示其不是数字值。

> 提示：请使用 isNaN() 全局函数来判断一个值是否是 NaN 值。

### 语法
Number.NaN

### 说明
Number.NaN 是一个特殊值，说明某些算术运算（如求负数的平方根）的结果不是数字。方法 parseInt() 和 parseFloat() 在不能解析指定的字符串时就返回这个值。对于一些常规情况下返回有效数字的函数，也可以采用这种方法，用 Number.NaN 说明它的错误情况。
JavaScript 以 NaN 的形式输出 Number.NaN。请注意，NaN 与其他数值进行比较的结果总是不相等的，包括它自身在内。因此，不能与 Number.NaN 比较来检测一个值是不是数字，而只能调用 isNaN() 来比较。
在 ECMAScript v1 和其后的版本中，还可以用预定义的全局属性 NaN 代替 Number.NaN。

> 提示：请使用 isNaN() 来判断一个值是否是数字。原因是 NaN 与所有值都不相等，包括它自己。

### 实例

用 NaN 指示某个值是否是数字：
```
<script type="text/javascript">

var Month=30;

if (Month < 1 || Month > 12) {
	Month = Number.NaN;
}

document.write(Month);

</script>
输出：
Nan
```


## 函数传参、arguments（可变参、不定参）

### arguments应用实例：任意数字求和

```
<script type="text/javascript">
function sum() {
	var result=0;
	var i=0;
	
	for(i=0;i<arguments.length;i++) {
		result+=arguments[i];
	}
	
	alert(result);
}

sum(12, 5, 7, 8, 12, 5, 7, 8, 12, 5, 7, 8, 12, 5, 7, 8, 12, 5, 7, 8, 12, 5, 7, 8, 12, 5, 7, 8, 12);
</script>


```

### 实例：CSS函数

```
<script type="text/javascript">
function getStyle(obj, attr) {
	if(obj.currentStyle) {
		return obj.currentStyle[attr]; // 获取非行间样式
	} else {
		return getComputedStyle(obj, false)[attr]; // 获取非行间样式
	}
}

function css() {
	if(arguments.length==2)	 { //获取
		return getStyle(arguments[0], arguments[1]); 
	}
	else if(arguments.length==3) { //设置
		arguments[0].style[arguments[1]]=arguments[2];
	}
}

window.onload=function () {
	var oBtn=document.getElementById('btn1');
	var oDiv=document.getElementById('div1');
	
	oBtn.onclick=function () {
		//css(oDiv, 'background', 'green');
		alert(css(oDiv, 'width'));
	}
}
</script>
```

## 数组定义、length使用

–	var arr=[12, 5, 8, 9];
–	var arr=new Array(12, 5, 8, 9);
没有任何差别
	

* length
	-	既可以获取，又可以设置
	-	例子：快速清空数组

```
<script type="text/javascript">
var arr=[1,2,3,4];

arr.length=10;

alert(arr[6]);
</script>
输出undefined
```

## push、pop、shift、unshift方法

*	添加
	-  push(元素)，从尾部添加
	-	unshift(元素)，从头部添加
*	删除
	-	pop()，从尾部弹出
	-	shift()，从头部弹出

## 排序：sort方法

sort([比较函数])，排序一个数组

## cancat、join方法

*	concat(数组2)
	-	连接两个数组
	
	```
	var arr1=[1,2,3];
	var arr2=[6,7,8];
	alert(arr1.concat(arr2));
	```
	
*	join(分隔符)
	-	用分隔符，组合数组元素，生成字符串

	```
	var arr=[1,2,3,4];
	alert(arr.join(':'));
	```
	
## split方法

```
"2:3:4:5".split(":")	//将返回["2", "3", "4", "5"]
"|a|b|c".split("|")	   //将返回["", "a", "b", "c"]
```

## splice方法：删除、插入、替换

splice(开始, 长度,元素…)

```
var arr=[1,2,3,4,5,6,7,8];

//1.中间删除
/*
arr.splice(2, 3);
alert(arr); // 1,2,6,7,8
*/

//2.中间插入
/*
arr.splice(5, 0, 'a', 'b', 'c');
alert(arr); // 1,2,3,4,5,a,b,6,7,8
*/

//3.替换
arr.splice(1, 2, 'a', 'b');
alert(arr); // 1,a,b,4,5,6,7,8
```


