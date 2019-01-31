# JavaScript笔记-DOM

- DOM基础
	* 什么是DOM
	* 浏览器支持情况
- DOM节点
	* childNodes	 nodeType  获取子节点children
	* parentNode 例子：点击链接，隐藏整个li
	* offsetParent 例子：获取元素在页面上的实际位置

- DOM节点(2)
	* 首尾子节点
			有兼容性问题
			firstChild、firstElementChild 
			lastChild 、lastElementChild
	* 兄弟节点
			有兼容性问题
			nextSibling、nextElementSibling
			previousSibling、previousElementSibling

- 元素属性操作
	* 第一种：oDiv.style.display=“block”;
	* 第二种：oDiv.style[“display”]=“block”;
	* 第三种：Dom方式
- DOM方式操作元素属性
	* 获取：getAttribute(名称)
	* 设置：setAttribute(名称, 值)
	* 删除：removeAttribute(名称)

- 用className选择元素
	* 如何用className选择元素
		选出所有元素
		通过className条件筛选
	* 封装成函数

- 创建DOM元素
	* createElement(标签名)		创建一个节点
	* appendChild(节点)			追加一个节点
			例子：为ul插入li
- 插入元素
	* insertBefore(节点, 原有节点) 在已有元素前插入
		例子：倒序插入li
- 删除DOM元素
	* removeChild(节点)			删除一个节点
		例子：删除li

- 文档碎片
	* 文档碎片可以提高DOM操作性能(理论上)
	* 文档碎片原理
	* document.createDocumentFragment()

## demo

```
window.onload=function ()
{
	var aA=document.getElementsByTagName('a');
	var i=0;
	
	for(i=0;i<aA.length;i++)
	{
		aA[i].onclick=function ()
		{
			this.parentNode.style.display='none';
		}
	}
}
```

```
<div id="div1" style="width:100px; height:100px; background:red; margin:100px;">
	<div onclick="alert(this.offsetParent.tagName)" id="div2" style="width:100px; height:100px; background:yellow; position:absolute; left:100px; top:100px;"></div>
</div>
```

```
window.onload=function ()
{
	var oLi=document.getElementById('li1');
	
	oLi.previousSibling.style.background='red';
}
```

```
window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	
	//IE
	//oUl.firstChild.style.background='red';
	
	//FF
	//oUl.firstElementChild.style.background='red';
	
	var oFirst=oUl.firstElementChild||oUl.firstChild;
	
	oFirst.style.background='red';
}
```

```
window.onload=function ()
{
	var oTxt=document.getElementById('txt1');
	
	//1
	//oTxt.value='123';
	
	//2
	//oTxt['value']='abc';
	
	//3
	//oTxt.setAttribute('value', 'rtertw');
	
	alert(oTxt.getAttribute('id')); // txt1
}
```

```
window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	
	alert(oUl.childNodes.length);
	//oUl.childNodes[0].style.background='red';
}
```

```
window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	
	alert(oUl.children.length);
}
```

```
<script type="text/javascript">
window.onload=function ()
{
	alert(document.body.childNodes[1].nodeType);
}
</script>
</head>

<body>
aaafsa
<span>fff</span>
</body>
```

```
function getByClass(oParent, sClass)
{
	var aEle=oParent.getElementsByTagName('*');
	var aResult=[];
	var i=0;
	
	for(i=0;i<aEle.length;i++)
	{
		if(aEle[i].className==sClass)
		{
			aResult.push(aEle[i]);
		}
	}
	
	return aResult;
}

window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	var aBox=getByClass(oUl, 'box');
	var i=0;
	
	for(i=0;i<aBox.length;i++)
	{
		aBox[i].style.background='yellow';
	}
}
```

```
window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	var aLi=oUl.getElementsByTagName('li');
	var i=0;
	
	for(i=0;i<aLi.length;i++)
	{
		if(aLi[i].className=='box')
		{
			aLi[i].style.background='red';
		}
	}
}
```

```
window.onload=function ()
{
	var aA=document.getElementsByTagName('a');
	var oUl=document.getElementById('ul1');
	var i=0;
	
	for(i=0;i<aA.length;i++)
	{
		aA[i].onclick=function ()
		{
			oUl.removeChild(this.parentNode);
		}
	}
}
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oTxt=document.getElementById('txt1');
	var oUl=document.getElementById('ul1');
	
	oBtn.onclick=function ()
	{
		var oLi=document.createElement('li');
		var aLi=oUl.getElementsByTagName('li');
		
		oLi.innerHTML=oTxt.value;
		
		if(aLi.length==0)
		{
			oUl.appendChild(oLi);
		}
		else
		{
			oUl.insertBefore(oLi, aLi[0]);
		}
	}
}
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oTxt=document.getElementById('txt1');
	var oUl=document.getElementById('ul1');
	
	oBtn.onclick=function ()
	{
		var oLi=document.createElement('li');
		var aLi=oUl.getElementsByTagName('li');
		
		oLi.innerHTML=oTxt.value;
		
		oUl.insertBefore(oLi, aLi[0]);
	}
}
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oUl=document.getElementById('ul1');
	
	oBtn.onclick=function ()
	{
		var iStart=new Date().getTime();
		var oFrag=document.createDocumentFragment();
		var i=0;
		
		for(i=0;i<100000;i++)
		{
			var oLi=document.createElement('li');
			
			oFrag.appendChild(oLi);
		}
		
		oUl.appendChild(oFrag);
		
		alert(new Date().getTime()-iStart);
	}
}
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oUl=document.getElementById('ul1');
	
	oBtn.onclick=function ()
	{
		var iStart=new Date().getTime();
		var i=0;
		
		for(i=0;i<100000;i++)
		{
			var oLi=document.createElement('li');
			
			oUl.appendChild(oLi);
		}
		
		alert(new Date().getTime()-iStart);
	}
}
```

```
window.onload=function ()
{
	var oBtn=document.getElementById('btn1');
	var oTxt=document.getElementById('txt1');
	var oUl=document.getElementById('ul1');
	
	oBtn.onclick=function ()
	{
		var oLi=document.createElement('li');
		
		oLi.innerHTML=oTxt.value;
		
		//父.appendChild(子节点)
		oUl.appendChild(oLi);
	}
}
```










