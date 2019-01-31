# JavaScript笔记-Event

- 什么是event对象
	* 用来获取事件的详细信息：鼠标位置、键盘按键
		例子：获取鼠标位置：clientX
		document的本质：document.childNodes[0].tagName
- 获取event对象(兼容性写法)
	* var oEvent=ev||event;
- 事件流
	* 事件冒泡
		取消冒泡：oEvent.cancelBubble=true
		例子：仿select控件
		DOM事件流

- 鼠标位置
	* 可视区位置：clientX、clientY
		例子1：跟随鼠标的Div
			消除滚动条的影响
			滚动条的意义——可视区与页面顶部的距离
	* 获取鼠标在页面的绝对位置
		封装函数
		例子2：一串跟随鼠标的Div

- keyCode
	* 获取用户按下键盘的哪个按键
	* 例子：键盘控制Div移动
- 其他属性
	* ctrlKey、shiftKey、altKey
	* 例子：提交留言
		回车 提交
		ctrl+回车 提交

- 默认行为
	* 什么是默认行为
- 阻止默认行为
	* 普通写法：return false;
	* 例子1.	屏蔽右键菜单
		弹出自定义右键菜单
	* 例子2.	只能输入数字的输入框
		keydown、keyup事件的区别

- 简易拖拽
	* 拖拽原理
		距离不变
		三个事件
- 靠谱拖拽
	* 原有拖拽的问题
		直接给document加事件
	* FF下，空Div拖拽Bug
		阻止默认事件
	* 防止拖出页面
		修正位置


## demo

```
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	alert(oDiv.offsetLeft);
};
```

```
document.onkeydown=function (ev)
{
	var oEvent=ev||event;
	
	alert(oEvent.keyCode);
};
```

```
document.onclick=function (ev)
{
	var oEvent=ev||event;
	
	alert(oEvent.clientX+', '+oEvent.clientY);
	//IE
	//alert(event.clientX+', '+event.clientY);
	
	//FF
	//alert(ev.clientX+', '+ev.clientY);
	
	/*if(ev)
	{
		alert(ev.clientX+', '+ev.clientY);
	}
	else
	{
		alert(event.clientX+', '+event.clientY);
	}*/
};
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oTxt1=document.getElementById('txt1');
	var oTxt2=document.getElementById('txt2');
	
	oBtn.onclick=function ()
	{
		oTxt1.value+=oTxt2.value+'\n';
		oTxt2.value='';
	};
	
	oTxt2.onkeydown=function (ev)
	{
		var oEvent=ev||event;
		
		if(oEvent.ctrlKey && oEvent.keyCode==13)
		{
			oTxt1.value+=oTxt2.value+'\n';
			oTxt2.value='';
		}
	};
};
```

```
window.onload=function ()
{
	var aDiv=document.getElementsByTagName('div');
	var i=0;
	
	document.onmousemove=function (ev)
	{
		var oEvent=ev||event;
		
		for(i=aDiv.length-1;i>0;i--)
		{
			aDiv[i].style.left=aDiv[i-1].style.left;
			aDiv[i].style.top=aDiv[i-1].style.top;
		}
		
		aDiv[0].style.left=oEvent.clientX+'px';
		aDiv[0].style.top=oEvent.clientY+'px';
	};
};
```

``` 
// 事件不传递，每个都响应
<div style="width:300px; height:300px; background:red;" onclick="alert(this.style.background);">
	<div style="width:200px; height:200px; background:green;" onclick="alert(this.style.background);">
    	<div style="width:100px; height:100px; background:#CCC;" onclick="alert(this.style.background);">
		</div>
	</div>
</div>
```

```
document.onclick=function ()
{
	//非Chrome
	//alert(document.documentElement.scrollTop);
	
	//chrome
	//alert(document.body.scrollTop);
	
	var scrollTop=document.documentElement.scrollTop||document.body.scrollTop;
};
```

```
document.onkeydown=function (ev)
{
	var oEvent=ev||event;
	var oDiv=document.getElementById('div1');
	
	//←		37
	//右		39
	
	if(oEvent.keyCode==37)
	{
		oDiv.style.left=oDiv.offsetLeft-10+'px';
	}
	else if(oEvent.keyCode==39)
	{
		oDiv.style.left=oDiv.offsetLeft+10+'px';
	}
};
```

```
var a=12||'abc';

alert(a); // 12
```

```
document.onmousemove=function (ev)
{
	var oEvent=ev||event;
	var oDiv=document.getElementById('div1');
	var scrollTop=document.documentElement.scrollTop||document.body.scrollTop;
	var scrollLeft=document.documentElement.scrollLeft||document.body.scrollLeft;
	
	oDiv.style.left=oEvent.clientX+scrollLeft+'px';
	oDiv.style.top=oEvent.clientY+scrollTop+'px';
};
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oDiv=document.getElementById('div1');
	
	oBtn.onclick=function (ev)
	{
		var oEvent=ev||event;
		oDiv.style.display='block';
		//alert('按钮被点了');
		
		oEvent.cancelBubble=true;
	};
	
	document.onclick=function ()
	{
		oDiv.style.display='none';
		//alert('页面被点了');
	};
};
```

```
document.onclick=function ()
{
	alert(document.documentElement.clientWidth); // 1280
};
```

```
window.onload=function ()
{
	var oTxt=document.getElementById('txt1');
	
	oTxt.onkeydown=function ()
	{
		return false;
	};
};
```

```
window.onload=function ()
{
	var oForm=document.getElementById('form1');
	
	oForm.onsubmit=function ()
	{
		return false;
	};
};
```

```
document.oncontextmenu=function (ev) 
{ // 鼠标右键点击
	var oEvent=ev||event;
	var oUl=document.getElementById('ul1');
	
	oUl.style.display='block';
	oUl.style.left=oEvent.clientX+'px';
	oUl.style.top=oEvent.clientY+'px';
	
	return false;
};

document.onclick=function ()
{
	var oUl=document.getElementById('ul1');
	
	oUl.style.display='none';
};
```

```
window.onload=function ()
{
	var oTxt=document.getElementById('txt1');
	
	oTxt.onkeydown=function (ev)
	{
		var oEvent=ev||event;
		
		//alert(oEvent.keyCode);
		
		//0		48
		//9		57
		//退格	8
		
		if(oEvent.keyCode!=8 && (oEvent.keyCode<48 || oEvent.keyCode>57))
		{
			return false;
		}
		
		//return false;
	};
};
```

```
// 拖拽3
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	var disX=0;
	var disY=0;
	
	oDiv.onmousedown=function (ev)
	{
		var oEvent=ev||event;
		
		disX=oEvent.clientX-oDiv.offsetLeft;
		disY=oEvent.clientY-oDiv.offsetTop;
		
		document.onmousemove=function (ev)
		{
			var oEvent=ev||event;
			var l=oEvent.clientX-disX;
			var t=oEvent.clientY-disY;
			
			if(l<0)
			{
				l=0;
			}
			else if(l>document.documentElement.clientWidth-oDiv.offsetWidth)
			{
				l=document.documentElement.clientWidth-oDiv.offsetWidth;
			}
			
			if(t<0)
			{
				t=0;
			}
			else if(t>document.documentElement.clientHeight-oDiv.offsetHeight)
			{
				t=document.documentElement.clientHeight-oDiv.offsetHeight;
			}
			
			oDiv.style.left=l+'px';
			oDiv.style.top=t+'px';
		};
		
		document.onmouseup=function ()
		{
			document.onmousemove=null;
			document.onmouseup=null;
		};
		
		return false;
	};
};
```

```
// 拖拽2
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	var disX=0;
	var disY=0;
	
	oDiv.onmousedown=function (ev)
	{
		var oEvent=ev||event;
		
		disX=oEvent.clientX-oDiv.offsetLeft;
		disY=oEvent.clientY-oDiv.offsetTop;
		
		document.onmousemove=function (ev)
		{
			var oEvent=ev||event;
			
			oDiv.style.left=oEvent.clientX-disX+'px';
			oDiv.style.top=oEvent.clientY-disY+'px';
		};
		
		document.onmouseup=function ()
		{
			document.onmousemove=null;
			document.onmouseup=null;
		};
		
		return false;
	};
};
```

```
// 拖拽1
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	var disX=0;
	var disY=0;
	
	oDiv.onmousedown=function (ev)
	{
		var oEvent=ev||event;
		
		disX=oEvent.clientX-oDiv.offsetLeft;
		disY=oEvent.clientY-oDiv.offsetTop;
		
		oDiv.onmousemove=function (ev)
		{
			var oEvent=ev||event;
			
			oDiv.style.left=oEvent.clientX-disX+'px';
			oDiv.style.top=oEvent.clientY-disY+'px';
		};
		
		oDiv.onmouseup=function ()
		{
			oDiv.onmousemove=null;
			oDiv.onmouseup=null;
		};
	};
};
```

```
// 拖拽0
window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	
	var disX=0;
	var disY=0;
	
	oDiv.onmousedown=function (ev)
	{
		var oEvent=ev||event;
		
		disX=oEvent.clientX-oDiv.offsetLeft;
		disY=oEvent.clientY-oDiv.offsetTop;
	};
	
	oDiv.onmousemove=function (ev)
	{
		var oEvent=ev||event;
		
		oDiv.style.left=oEvent.clientX-disX+'px';
		oDiv.style.top=oEvent.clientY-disY+'px';
	};
};
```

```
document.oncontextmenu=function ()
{
	return false;
};
```

