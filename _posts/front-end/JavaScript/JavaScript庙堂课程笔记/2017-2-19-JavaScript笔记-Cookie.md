# JavaScript笔记-Cookie

- 什么是cookie
	* 页面用来保存信息
		比如：自动登录、记住用户名
	* cookie的特性
		同一个网站中所有页面共享一套cookie
		数量、大小有限
		过期时间
	* JS中使用cookie
		document.cookie

- cookie的使用
	* 设置cookie
		格式：名字=值
		不会覆盖
		过期时间：expires=时间
		日期对象的使用
		封装函数
	* 读取cookie
		字符串分割
	* 删除cookie
		已经过期

- cookie的使用
	* 例子 用cookie记录上一次Div的位置
		鼠标抬起——记录位置
		window.onload——读取位置
	* 用cookie记录上次登录的用户名
		提交时——记录用户名
		window.onload——读取用户名

## demo

```
function setCookie(name, value, iDay)
{
	var oDate=new Date();
	
	oDate.setDate(oDate.getDate()+iDay);
	
	document.cookie=name+'='+value+';expires='+oDate;
}

function getCookie(name)
{
	//'username=abc; password=123456; aaa=123; bbb=4r4er'
	var arr=document.cookie.split('; ');
	var i=0;
	
	//arr->['username=abc', 'password=123456', ...]
	
	for(i=0;i<arr.length;i++)
	{
		//arr2->['username', 'abc']
		var arr2=arr[i].split('=');
		
		if(arr2[0]==name)
		{
			return arr2[1];
		}
	}
	
	return '';
}

function removeCookie(name)
{
	setCookie(name, '1', -1);
}

alert(getCookie('username'));
removeCookie('username');
alert(getCookie('username'));

/*setCookie('username', 'abc', 30);
setCookie('password', '123456', 300);
setCookie('aaa', '123', 300);
setCookie('bbb', '4r4er', 300);*/

/*var oDate=new Date();

oDate.setDate(oDate.getDate()+30);

document.cookie="user=blue;expires="+oDate;
document.cookie="pass=123";*/

//alert(document.cookie);
```

```
var oDate=new Date();

oDate.setDate(oDate.getDate()+30);

document.cookie="user=blue;expires="+oDate;
document.cookie="pass=123";

alert(document.cookie);
```

```
var oDate=new Date();

oDate.setDate(oDate.getDate()+30);

alert(oDate.getFullYear()+'-'+(oDate.getMonth()+1)+'-'+oDate.getDate());
```

```
// cookie结合拖拽
function setCookie(name, value, iDay)
{
	var oDate=new Date();
	
	oDate.setDate(oDate.getDate()+iDay);
	
	document.cookie=name+'='+value+';expires='+oDate;
}

function getCookie(name)
{
	//'username=abc; password=123456; aaa=123; bbb=4r4er'
	var arr=document.cookie.split('; ');
	var i=0;
	
	//arr->['username=abc', 'password=123456', ...]
	
	for(i=0;i<arr.length;i++)
	{
		//arr2->['username', 'abc']
		var arr2=arr[i].split('=');
		
		if(arr2[0]==name)
		{
			return arr2[1];
		}
	}
	
	return '';
}

function removeCookie(name)
{
	setCookie(name, '1', -1);
}

window.onload=function ()
{
	var oDiv=document.getElementById('div1');
	var disX=0;
	var disY=0;
	
	var x=getCookie('x');
	var y=getCookie('y');
	
	if(x)
	{
		oDiv.style.left=x+'px';
		oDiv.style.top=y+'px';
	}
	
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
			
			setCookie('x', oDiv.offsetLeft, 5);
			setCookie('y', oDiv.offsetTop, 5);
		};
		
		return false;
	};
};
```

```
function setCookie(name, value, iDay)
{
	var oDate=new Date();
	
	oDate.setDate(oDate.getDate()+iDay);
	
	document.cookie=name+'='+value+';expires='+oDate;
}

function getCookie(name)
{
	//'username=abc; password=123456; aaa=123; bbb=4r4er'
	var arr=document.cookie.split('; ');
	var i=0;
	
	//arr->['username=abc', 'password=123456', ...]
	
	for(i=0;i<arr.length;i++)
	{
		//arr2->['username', 'abc']
		var arr2=arr[i].split('=');
		
		if(arr2[0]==name)
		{
			return arr2[1];
		}
	}
	
	return '';
}

function removeCookie(name)
{
	setCookie(name, '1', -1);
}

window.onload=function ()
{
	var oForm=document.getElementById('form1');
	var oUser=document.getElementsByName('user')[0];
	var oBtnClear=document.getElementsByTagName('a')[0];
	
	oForm.onsubmit=function ()
	{
		setCookie('user', oUser.value, 30);
	};
	
	oUser.value=getCookie('user');
	
	oBtnClear.onclick=function ()
	{
		removeCookie('user');
		oUser.value='';
	};
};
```


