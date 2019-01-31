# JavaScript笔记-运动基础

- 运动基础
	* 让Div运动起来
	* 速度——物体运动的快慢
	* 运动中的Bug
		不会停止
		速度取某些值会无法停止
		到达位置后再点击还会运动
		重复点击速度加快
- 匀速运动
	* 速度不变

- 运动框架
	* 在开始运动时，关闭已有定时器
	* 把运动和停止隔开(if/else)
- 运动框架实例
	* 例子1：“分享到”侧边栏
		通过目标点，计算速度值
	* 例子2：淡入淡出的图片
		用变量存储透明度

- 缓冲运动
	* 逐渐变慢，最后停止
	* 距离越远速度越大
		速度由距离决定
		速度=(目标值-当前值)/缩放系数
	* 例子：缓冲菜单
		Bug：速度取整
		跟随页面滚动的缓冲侧边栏
			潜在问题：目标值不是整数时

- 运动终止条件
	* 匀速运动：距离足够近
	* 缓冲运动：两点重合

## demo

```
var timer=null;

function startMove()
{
	var oDiv=document.getElementById('div1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=5;
		
		if(oDiv.offsetLeft>=300)	//是否到达终点
		{
			clearInterval(timer);	//到达终点
		}
		else
		{
			oDiv.style.left=oDiv.offsetLeft+iSpeed+'px';	//到达之前
		}
	}, 30);
}
```

```
alert(Math.abs(5));
```

```
var timer=null;

function startMove(iTarget)
{
	var oDiv=document.getElementById('div1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=0;
		
		if(oDiv.offsetLeft<iTarget)
		{
			iSpeed=7;
		}
		else
		{
			iSpeed=-7;
		}
		
		if(Math.abs(oDiv.offsetLeft-iTarget)<7)	//是否到达终点
		{
			clearInterval(timer);	//到达终点
			
			oDiv.style.left=iTarget+'px';
		}
		else
		{
			oDiv.style.left=oDiv.offsetLeft+iSpeed+'px';	//到达之前
		}
	}, 30);
}
```

``
window.onscroll=function ()
{
	var oDiv=document.getElementById('div1');
	var scrollTop=document.documentElement.scrollTop||document.body.scrollTop;
	
	//oDiv.style.top=scrollTop+(document.documentElement.clientHeight-oDiv.offsetHeight)/2+'px';
	var t=scrollTop+(document.documentElement.clientHeight-oDiv.offsetHeight)/2;
	
	startMove(parseInt(t));
}

var timer=null;

function startMove(iTarget)
{
	var oDiv=document.getElementById('div1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=(iTarget-oDiv.offsetTop)/8;
		iSpeed=iSpeed>0?Math.ceil(iSpeed):Math.floor(iSpeed);
		
		if(oDiv.offsetTop==iTarget)
		{
			clearInterval(timer);
		}
		else
		{
			oDiv.style.top=oDiv.offsetTop+iSpeed+'px';
		}
		
		txt1.value=oDiv.offsetTop+',目标：'+iTarget;
	}, 30);
}
```

```
window.onscroll=function ()
{
	var oDiv=document.getElementById('div1');
	var scrollTop=document.documentElement.scrollTop||document.body.scrollTop;
	
	oDiv.style.top=scrollTop+(document.documentElement.clientHeight-oDiv.offsetHeight)/2+'px';
}
```

```
//alert(Math.ceil(-12.5));
alert(Math.floor(-0.8)); // -1
```

```
var timer=null;

function startMove(iTarget)
{
	var oDiv=document.getElementById('div1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=(iTarget-oDiv.offsetLeft)/8;
		iSpeed=iSpeed>0?Math.ceil(iSpeed):Math.floor(iSpeed);
		/*if(iSpeed>0)
		{
			iSpeed=Math.ceil(iSpeed);
		}
		else
		{
			iSpeed=Math.floor(iSpeed);
		}*/
		
		if(oDiv.offsetLeft==iTarget)	//是否到达终点
		{
			clearInterval(timer);	//到达终点
		}
		else
		{
			oDiv.style.left=oDiv.offsetLeft+iSpeed+'px';	//到达之前
		}
		
		document.title=oDiv.offsetLeft+',speed='+iSpeed;
	}, 30);
}
```

```
var timer=null;

function startMove(iTarget)
{
	var oDiv=document.getElementById('div1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=(iTarget-oDiv.offsetLeft)/8;
		
		if(oDiv.offsetLeft>=iTarget)	//是否到达终点
		{
			clearInterval(timer);	//到达终点
		}
		else
		{
			oDiv.style.left=oDiv.offsetLeft+iSpeed+'px';	//到达之前
		}
		
		document.title=oDiv.offsetLeft+',speed='+iSpeed;
	}, 30);
}
```

```
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	oDiv.onmouseover=function ()
	{
		startMove(0);
	}
	
	oDiv.onmouseout=function ()
	{
		startMove(-100);
	}
}
var timer=null;

function startMove(iTarget)
{
	var oDiv=document.getElementById('div1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=0;
		
		if(oDiv.offsetLeft<iTarget)
		{
			iSpeed=10;
		}
		else
		{
			iSpeed=-10;
		}
		
		if(oDiv.offsetLeft==iTarget)
		{
			clearInterval(timer);
		}
		else
		{
			oDiv.style.left=oDiv.offsetLeft+iSpeed+'px';
		}
	}, 30);
}
```

```
window.onload=function ()
{
	var oImg=document.getElementById('img1');
	
	oImg.onmouseover=function ()
	{
		startMove(100);
	}
	
	oImg.onmouseout=function ()
	{
		startMove(30);
	}
}
var timer=null;

var alpha=30;

function startMove(iTarget)
{
	var oImg=document.getElementById('img1');
	
	clearInterval(timer);
	timer=setInterval(function (){
		var iSpeed=0;
		
		if(alpha<iTarget)
		{
			iSpeed=1;
		}
		else
		{
			iSpeed=-1;
		}
		
		if(alpha==iTarget)
		{
			clearInterval(timer);
		}
		else
		{
			alpha+=iSpeed;
			
			oImg.style.filter='alpha(opacity:'+alpha+')';
			oImg.style.opacity=alpha/100;
			
			document.title=alpha;
		}
	}, 30);
}
```







