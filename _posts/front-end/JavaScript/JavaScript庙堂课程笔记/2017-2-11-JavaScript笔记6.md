# JavaScript笔记--2017-02-11

## ==

```
var a='12';
var b=12;

alert(a==b);	//先把两边的东西转成一样的类型，然后再比较
//alert(a===b);	//不转换，直接比较
```

## agruments

```
<!--
function show()
{
	//arguments->[12, 6]
	alert(arguments[1]);
}
-->

function show(a, b)
{
	alert(arguments[0]);
}

show(12, 6);
```

## typeof

```
var a=12;

alert(typeof a);	//number

a='abc';
//alert(typeof a);	//string

a=true;
//alert(typeof a);	//boolean	布尔

/*window.onload=function ()
{
	a=document.getElementById('div1');
	
	alert(typeof a);	//object
};*/

a=function ()
{
	alert('abc');
};

//alert(typeof a);	//function

var b;
alert(typeof b);	//undefined
/*
undefined	1.你真的就没定义	2.虽然定义了，但是没给值
*/
```

## NaN

```
var a='abc12';

alert(parseInt(a));
//NaN	Not A Number	不是数字——非数字

var a=parseInt('abc');
alert(isNaN(a)); // true
```

## parseInt

```
var a='12px34';

alert(parseInt(a)); // 12
```

## parseFloat

```
var a='12.6';

//alert(parseInt(a));
alert(parseFloat(a)); // 12.6
```

## array 

```
/*var obj={a: 5, b: 6, c:'abc', d: [1,2,3,4]};

alert(obj.d[0]);*/

var arr=[{a: 5, b: 12}, {a: 6, b: 19}];

alert(arr[1].b); // 19
```

## map

```
var obj={a: 5, b: 3, c: 98};
var attr='';

for(attr in obj)
{
	alert(attr+'='+obj[attr]);
}
// a=5,b=3,c=98
```

## 获取css style

```
var oDiv=document.getElementById('div1');
	
	//获取计算后的样式(当前样式、最终样式)
	//IE
	//alert(oDiv.currentStyle.width);
	
	//FF
	//alert(getComputedStyle(oDiv, false).width);
	
	if(oDiv.currentStyle) { //IE
		alert(oDiv.currentStyle.width);
	} else { //FF
		alert(getComputedStyle(oDiv, false).width);
	}
	
	// 100px
```

```
function getStyle(obj, attr)
{
	if(obj.currentStyle)
	{
		return obj.currentStyle[attr];
	}
	else
	{
		return getComputedStyle(obj, false)[attr];
	}
}

function css(obj, attr, value)
{
	if(arguments.length==2)	//获取
	{
		return getStyle(obj, attr);
	}
	else if(arguments.length==3)	//设置
	{
		obj.style[attr]=value;
	}
}

window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oDiv=document.getElementById('div1');
	
	oBtn.onclick=function ()
	{
		css(oDiv, 'background', 'green');
		//alert(css(oDiv, 'width'));
		alert(getStyle(oDiv, 'backgroundColor')); // rgb(255,0,0)
	}
}
```

## split

```
var str='12-4-5-8';
var arr=str.split('-');

//'12-4-5'.split('-')	['12','4','5']

alert(arr[0]+arr[1]); // 124
```

## return 

```
function show()
{
	return ;
} 

alert(show()); // undefined
```

## array length

```
var arr=[1,2,3,4];

arr.length=10;

alert(arr[6]); // undefined
```

## sort

```
var arr=[96, 8, 12, 72, 33, 118];

arr.sort(function (num1, num2){
	return num1-num2;
});

alert(arr); // 8,12,33,72,96,118
```

```
var arr=['float', 'zindex', 'xy', 'absolute', 'blue', 'leo'];

arr.sort();

alert(arr); //["absolute", "blue", "float", "leo", "xy", "zindex"]
```

## true false

```
var a;

//真的：true、非零数字、非空字符串、非空对象
//假的：false、数字零、空字符串、空对象、undefined

if(a)
{
	alert('真的');
}
else
{
	alert('假的');
}
```


