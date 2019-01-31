# JavaScript笔记-运动高级

- 回调函数
	* 运动停止时，执行函数
	* 运动停止时，开始下一次运动
	* 例子：土豆网右下角菜单

- 多个值同时变化
	* setStyle同时设置多个属性
		参数传递
			Json的使用
			for in遍历属性
	* 运用到运动框架
	* 检测运动停止
		标志变量
	* 例子：伸缩同时淡入淡出的菜单

- 运动框架演变过程
	* startMove(iTarget)			运动框架
	* startMove(obj, iTarget)		多物体
	* startMove(obj, attr, iTarget)	任意值
	* startMove(obj, attr, iTarget, fn)	链式运动
	* startMove(obj, json)		多值运动
	* startMove(obj, json, fn)		完美运动框架

- 运动框架应用
	* 例子：多图片展开、收缩
		布局转换
	* 例子：新浪微博
		链式运动
	* 无缝滚动
		走到一半拉回来

# demo

```
window.onload=function ()
{
	var oUl=document.getElementById('ul1');
	var aLi=oUl.getElementsByTagName('li');
	var i=0;
	
	for(i=0;i<aLi.length;i++)
	{
		aLi[i].onmouseover=function ()
		{
			var oDiv=this.getElementsByTagName('div')[0];
			
			oDiv.style.height='auto';
			var iHeight=oDiv.offsetHeight;
			oDiv.style.height=0;

			startMove(oDiv, {opacity: 100, height:iHeight});
		};
		
		aLi[i].onmouseout=function ()
		{
			var oDiv=this.getElementsByTagName('div')[0];

			startMove(oDiv, {opacity: 0, height: 0});
		};
	}
};
```



