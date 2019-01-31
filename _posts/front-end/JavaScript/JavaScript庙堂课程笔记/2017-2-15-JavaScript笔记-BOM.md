# JavaScript笔记-BOM

	-	open
	-	close
	-	常用属性
		*  window.navigator.userAgent
		*	window.location
	-  可视区尺寸
		*	document.documentElement.clientWidth
		*	document.documentElement.clientHeight
	-	滚动距离
		*	document.body.scrollTop
		*	document.documentElement.scrollTop
	-	系统对话框
		*	警告框：alert(“内容”)，没有返回值
		*	选择框：confirm(“提问的内容”)，返回boolean
		*	输入框：prompt()，返回字符串或null
	-	window对象常用事件
		*	onload
		*	onscroll
		*	onresize

## userAgent

```
alert(window.navigator.userAgent); // Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_2) AppleWebKit/602.3.12 (KHTML, like Gecko) Version/10.0.2 Safari/602.3.12
```

## open

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		window.open('http://www.miaov.com/', '_self');
	};
};
```

```
window.onload=function ()
{
	var oTxt=document.getElementById('txt1');
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		var oNewWin=window.open('about:blank');
		
		oNewWin.document.write(oTxt.value);
	};
};
```

## write

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		//清空当前页面，并输出东西
		document.write('sdf');
	};
};
```

## close

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		window.close();
	};
};
```

## 回到顶部

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var bSys=true;
	var timer=null;
	
	//如何检测用户拖动了滚动条
	window.onscroll=function ()
	{
		if(!bSys)
		{
			clearInterval(timer);
		}
		bSys=false;
	};
	
	oBtn.onclick=function ()
	{
		setInterval(function (){
			var scollTop=document.documentElement.scrollTop||document.body.scrollTop;
			var iSpeed=Math.floor(-scollTop/8);
			
			if(scrollTop==0)
			{
				clearInterval(timer);
			}
			document.documentElement.scrollTop=document.body.scrollTop=scollTop+iSpeed;
		}, 30);
	};
};
```

## confirm/prompt

```
//alert('abc');

/*var b=confirm('今天下雨了吗？');

alert(b);*/

var str=prompt('请输入你的姓名', 'blue');
alert(str);
```

