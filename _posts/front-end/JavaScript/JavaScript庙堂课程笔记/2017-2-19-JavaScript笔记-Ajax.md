# JavaScript笔记-Ajax

- 什么是服务器
	* 网页浏览过程分析
	* 如何配置自己的服务器程序（AMP）
- 什么是Ajax
	* 无刷新数据读取
		用户注册、在线聊天室
		异步、同步

- 使用Ajax
	* 基础：请求并显示静态TXT文件
		字符集编码
		缓存、阻止缓存
	* 动态数据：请求JS（或json）文件
		eval的使用
		DOM创建元素
	* 局部刷新：请求并显示部分网页文件

- HTTP请求方法
	* GET——用于获取数据（如：浏览帖子）
	* POST——用于上传数据（如：用户注册）
	* GET、POST的区别
		get是在url里传数据：安全性、容量
		缓存

- 创建Ajax对象
	* ActiveXObject("Microsoft.XMLHTTP")
	* XMLHttpRequest()
- 连接服务器
	* open(方法, 文件名, 异步传输)
		同步和异步
- 发送请求
	* send()

- 请求状态监控
	* onreadystatechange事件
		readyState属性：请求状态
			0	（未初始化）还没有调用open()方法
			1	（载入）已调用send()方法，正在发送请求
			2	（载入完成）send()方法完成，已收到全部响应内容
			3	（解析）正在解析响应内容
			4	（完成）响应内容解析完成，可以在客户端调用了
		status属性：请求结果
		responseText

- 数据类型
	* 什么叫数据类型——英语、中文
	* XML、Json
- 字符集
	* 所有文件字符集相同

## demo

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		//ajax能且仅能 从服务器读取文件
		//alert('abc.txt?t='+new Date().getTime());
		ajax('abc.txt?t='+new Date().getTime(), function (str){
			alert(str);
		});
	};
};
```

```
window.onload=function ()
{
	var aBtn=document.getElementsByTagName('input');
	var oDiv=document.getElementById('div1');
	var i=0;
	
	for(i=0;i<aBtn.length;i++)
	{
		aBtn[i].index=i;
		aBtn[i].onclick=function ()
		{
			ajax(this.index+1+'.txt', function (str){
				oDiv.innerHTML=str;
			});
		};
	}
};
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		ajax('data.txt', function (str){
			//str->'[1,2,3,4]'
			//alert(str);
			var arr=eval(str);
			
			alert(arr[3]);
		});
	};
};
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		ajax('data.json', function (str){
			var arr=eval(str);
			
			alert(arr[0].b);
		});
	};
};
```

```
window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	var aBtn=document.getElementsByTagName('a');
	var i=0;
	
	for(i=0;i<aBtn.length;i++)
	{
		aBtn[i].index=i;
		aBtn[i].onclick=function ()
		{
			ajax('page'+(this.index+1)+'.txt', function (str){
				var aData=eval(str);
				
				oUl.innerHTML='';
				for(i=0;i<aData.length;i++)
				{
					var oLi=document.createElement('li');
					
					oLi.innerHTML='<strong>'+aData[i].user+'</strong><i>'+aData[i].pass+'</i>';
					
					oUl.appendChild(oLi);
				}
			});
		};
	}
};
```

```
//eval就是计算字符串里的值

/*var str='54-8*6+4';

alert(eval(str));*/

/*var str='[12,2,3]';
var arr=eval(str);

alert(arr[0]);*/

/*var str='alert("abc");';

eval(str);
*/

/*var str="function show(){alert('abc');}";
eval(str);
show();*/
```

```
/*var a=12;

alert(a);*/
/*function show()
{
	alert('a');
}

window.show();*/

//var a=12;

//用一个不存在的变量：出错
//用一个不存在的属性：undefined

alert(window.a);
alert(a);
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	
	oBtn.onclick=function ()
	{
		//1.创建ajax对象
		//IE6以上
		/*var oAjax=new XMLHttpRequest();
		
		alert(oAjax);*/
		
		//IE6
		/*var oAjax=new ActiveXObject("Microsoft.XMLHTTP");
		
		alert(oAjax);*/
		var oAjax=null;
		
		if(window.XMLHttpRequest)
		{
			oAjax=new XMLHttpRequest();
		}
		else
		{
			oAjax=new ActiveXObject("Microsoft.XMLHTTP");
		}
		
		//2.连接服务器
		//open(方法, url, 是否异步)
		oAjax.open('GET', 'abc.txt', true);
		
		//3.发送请求
		oAjax.send();
		
		//4.接收返回
		//OnReadyStateChange
		oAjax.onreadystatechange=function ()
		{
			if(oAjax.readyState==4)
			{
				if(oAjax.status==200)
				{
					alert('成功：'+oAjax.responseText);
				}
				else
				{
					alert('失败');
				}
			}
		};
	};
};
```



