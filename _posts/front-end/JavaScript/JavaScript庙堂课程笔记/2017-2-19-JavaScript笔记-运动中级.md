# JavaScript笔记-运动中级

- 多个物体同时运动
	* 例子：多个Div，鼠标移入变宽
		单定时器，存在问题
		每个Div一个定时器
- 多物体运动框架
	* 定时器作为物体的属性
	* 参数的传递：物体、目标值
	* 例子：多个Div淡入淡出
		所有东西都不能公用
		属性与运动对象绑定：速度、其他属性值（如透明度等）

- offset属性的Bug
	* 有边框的Div变宽
		用currentStyle代替offset
- 原有运动框架的问题
	* 只能让某个值运动起来
	* 如果想让其他值运动起来，要修改程序
- 扩展的运动框架
	* 运动属性作为参数
	* 封装opacity
		小数的问题

- 效果思路
	* 两边的按钮——淡入淡出
	* 大图下拉——层级、高度变化
	* 下方的li——多物体淡入淡出
	* 下方的Ul——位置计算
- 左右按钮
	* 淡入淡出
		鼠标移到按钮上，按钮会消失
			层级问题
			按钮和遮罩上都得加上事件

- 下方Li效果
	* 点击切换大图——选项卡
	* Li淡入淡出——移入移出
	* Ul移动——位置计算
- 大图片切换
	* 图片层级——zIndex一直加1
	* 图片下拉效果(运动框架)
		可以改为淡入淡出
- 加入自动播放
	* 和选项卡一样

## demo

```
window.onload=function ()
{
	var aDiv=document.getElementsByTagName('div');
	var i=0;
	
	for(i=0;i<aDiv.length;i++)
	{
		aDiv[i].alpha=30;
		aDiv[i].onmouseover=function ()
		{
			startMove(this, 100);
		}
		
		aDiv[i].onmouseout=function ()
		{
			startMove(this, 30);
		}
	}
}
//var alpha=30;

function startMove(obj, iTarget)
{
	clearInterval(obj.timer);
	obj.timer=setInterval(function (){
		var iSpeed=(iTarget-obj.alpha)/8;
		iSpeed=iSpeed>0?Math.ceil(iSpeed):Math.floor(iSpeed);
		
		if(obj.alpha==iTarget)
		{
			clearInterval(obj.timer);
		}
		else
		{
			obj.alpha+=iSpeed;
			
			obj.style.filter='alpha(opacity:'+obj.alpha+')';
			obj.style.opacity=obj.alpha/100;
		}
	}, 30);
}
```

```
window.onload=function ()
{
	var aDiv=document.getElementsByTagName('div');
	
	var i=0;
	
	for(i=0;i<aDiv.length;i++)
	{
		aDiv[i].timer=null;
		aDiv[i].onmouseover=function ()
		{
			startMove(this, 300);
		}
		
		aDiv[i].onmouseout=function ()
		{
			startMove(this, 100);
		}
	}
}

function startMove(obj, iTarget)
{
	clearInterval(obj.timer);
	obj.timer=setInterval(function (){
		var iSpeed=(iTarget-obj.offsetWidth)/8;
		iSpeed=iSpeed>0?Math.ceil(iSpeed):Math.floor(iSpeed);
		
		if(obj.offsetWidth==iTarget)
		{
			clearInterval(obj.timer);
		}
		else
		{
			obj.style.width=obj.offsetWidth+iSpeed+'px';
		}
	}, 30)
}
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

setInterval(function (){
	var oDiv=document.getElementById('div1');
	
	//oDiv.style.width=oDiv.offsetWidth-1+'px';
	oDiv.style.width=parseInt(getStyle(oDiv, 'width'))-1+'px';
}, 30);
```

```
var a=3;
var b=3.00000000000000000001;
alert(a==b); // true
```

```
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	oDiv.onmouseover=function ()
	{
		startMove(oDiv, 'opacity', 100);
	}
	
	oDiv.onmouseout=function ()
	{
		startMove(oDiv, 'opacity', 30);
	}
}

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

function startMove(obj, attr, iTarget)
{
	clearInterval(obj.timer);
	obj.timer=setInterval(function (){
		var iCur=0;
		
		if(attr=='opacity')
		{
			iCur=parseInt(parseFloat(getStyle(obj, attr))*100);
		}
		else
		{
			iCur=parseInt(getStyle(obj, attr));
		}
		
		var iSpeed=(iTarget-iCur)/8;
		iSpeed=iSpeed>0?Math.ceil(iSpeed):Math.floor(iSpeed);
		
		if(iCur==iTarget)
		{
			clearInterval(obj.timer);
		}
		else
		{
			if(attr=='opacity')
			{
				obj.style.filter='alpha(opacity:'+(iCur+iSpeed)+')';
				obj.style.opacity=(iCur+iSpeed)/100;
				
				document.getElementById('txt1').value=obj.style.opacity;
			}
			else
			{
				obj.style[attr]=iCur+iSpeed+'px';
			}
		}
	}, 30)
}
```

```
window.onload=function ()
{
	var aDiv=document.getElementsByTagName('div');
	
	aDiv[0].onclick=function ()
	{
		startMove(this, 'width', 300);
	}
	
	aDiv[1].onclick=function ()
	{
		startMove(this, 'height', 200);
	}
	
	aDiv[2].onclick=function ()
	{
		startMove(this, 'fontSize', 100);
	}
	
	aDiv[3].onclick=function ()
	{
		startMove(this, 'borderWidth', 50);
	}
}

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

function startMove(obj, attr, iTarget)
{
	clearInterval(obj.timer);
	obj.timer=setInterval(function (){
		var iCur=parseInt(getStyle(obj, attr));
		var iSpeed=(iTarget-iCur)/8;
		iSpeed=iSpeed>0?Math.ceil(iSpeed):Math.floor(iSpeed);
		
		if(iCur==iTarget)
		{
			clearInterval(obj.timer);
		}
		else
		{
			obj.style[attr]=iCur+iSpeed+'px';
		}
	}, 30)
}
```






