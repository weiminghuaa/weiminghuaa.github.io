# JavaScript笔记--2017-02-03.md

## window.onload

- 必须等到页面内包括图片内的所有元素加载完毕后执行
- 不能同时编写多个，如果有多个，只执行一个
- 原生JavaScript事件

## 鼠标移入提示

```
<label onmouseover="document.getElementById('div1').style.display='block';"  onmouseout="document.getElementById('div1').style.display='none';">
<input type="checkbox"/> 自动登录
</label>

<div id="div1"> 
不要在网吧点击 
</div>
```

## 网页换肤

```
<link id="link1" href="css1.css" rel="stylesheet" type="text/css" />

<input id="btn1" type="button" value="皮肤1" onclick="document.getElementById('link1').href='css1.css';" />
<input id="btn2" type="button" value="皮肤2" onclick="document.getElementById('link1').href='css2.css';" />
```

## 改变div样式

```
<div id="div1" onmouseover="toGreen()" onmouseout="toRed()">

<script type="text/javascript">
function toGreen() {
	var oDiv1=document.getElementById('div1');
	oDiv1.style.width='200px';
	oDiv1.style.height='200px';
	oDiv1.style.background='green';
}

function toRed() {
	var oDiv1=document.getElementById('div1');
	oDiv1.style.width='100px';
	oDiv1.style.height='100px';
	oDiv1.style.background='red';
}
</script>
```

## 切换样式

```
<script type="text/javascript">
function showHideUl() {
	var oDiv=document.getElementById('drop');
	var oUl=oDiv.getElementsByTagName('ul')[0];
	var oH2=oDiv.getElementsByTagName('h2')[0];
	
	if(oUl.style.display === 'none') {
		oUl.style.display='block';
		oH2.className='up';
	} else {
		oUl.style.display='none';
		oH2.className='down';
	}
}
</script>
```

## 评分
图片：
![sta](media/star.gif)
效果：
![屏幕快照 2017-02-03 22.06.16](media/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-02-03%2022.06.16.png)


```
html:
<div id="pingfen">
	<ul>
    	<li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
    </ul>
</div>

css:
#pingfen {
    width: 135px;
    margin: 10px auto;
    height: 28px;
}

#pingfen li {
    width: 27px;
    float: left;
    height: 28px;
    cursor: pointer;
    background: url(star.gif) no-repeat 0 0;
    list-style: none;
}

js:
window.onload=function(){
	var oPf=document.getElementById('pingfen');
	var aLi=oPf.getElementsByTagName('li');
	var i=0;
	
	for(i=0;i<aLi.length;i++){
		aLi[i].index=i;
		aLi[i].onmouseover=function(){
			for(i=0;i<aLi.length;i++){
				if(i<=this.index)
				{
					aLi[i].style.background="url(star.gif) no-repeat 0 -29px";
				}
				else
				{
					aLi[i].style.background="url(star.gif) no-repeat 0 0";
				}
			}
		};
		
		aLi[i].onmousedown=function ()
		{
			alert('提交成功:'+(this.index+1)+'分');
		};
	}
};
```

## 综合案例

[淘宝商品列表](file:///Users/weiminghua/Documents/JavaScript/part1/0/demo2/淘宝商品列表/miaov_demo.html)

里面的代码非常基础，值得研究，尤其是排序。

