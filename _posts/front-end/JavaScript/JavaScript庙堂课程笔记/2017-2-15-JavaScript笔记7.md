# JavaScript笔记-20170115

## toString

```
show.toString=function ()
{
	return '就不让你看';
};

alert(show);
```

```
var num=255;
alert('0x'+num.toString(16)); // 0xff
```

## function

```
/*function show()
{
	alert('a');
}*/

/*var show=function ()
{
	alert('a');
};*/

var show=new Function('alert("a");');
show();
```

```
var str='aaaaaa';

str.length=1;

alert(str); // aaaaaa
```

```
var str1='abc';
var str2=new String('abc');

alert(typeof str1); // string
alert(typeof str2); // object
```

```
function hasContain(arr, num)
{
	var i=0;
	
	for(i=0;i<arr.length;i++)
	{
		if(arr[i]==num)
		{
			return true;
		}
	}
	
	return false;
}

var arr=[1,2,3,4,1,5,6,4,8];
var aResult=[];
var i=0;

for(i=0;i<arr.length;i++)
{
	if(!hasContain(aResult, arr[i]))
	{
		aResult.push(arr[i]);
	}
}

alert(aResult);// 1,2,3,4,5,6,8
```

```
var arr1=[1,2,3];
var arr2=arr1.concat([]);

arr2.push(4);

alert(arr1); // 1,2,3
```

```
var arr=[1,2,3];

arr.toString=function ()
{
	return 'abc';
};

alert(arr); // abc
alert(arr.toString()); // abc
```

```
var arr=[12,3,4,5];		//性能略高
//var arr=new Array(12,3,4,5);	//性能略低

arr.length=2;

alert(arr); // 12,3
```

## splice

```
var arr=[1,2,3,4,5,6,7,8,9];

//删除
/*arr.splice(2, 3);
alert(arr);*/

//插入
/*arr.splice(2, 0, 'a', 'b', 'c');
alert(arr);*/

//替换
arr.splice(2, 3, 'a', 'b', 'c');
alert(arr); // 1,2,a,b,c,6,7,8,9
```

## reverse

```
var arr=[1,2,3,4,5];
alert(arr.reverse());
```

## compare

```
var arr=['张三', '石川', '刘伟', '莫涛', '杜鹏', '阿杜', '毕加索'];

arr.sort(function (str1, str2){
	return str1.localeCompare(str2);
});

//只要能用原生方法的时候，就不要自己写

alert(arr);
```

```
var arr=[12,78, 335, 21, 2];

//arr.sort();	//默认的情况下，他会把所有的东西都当做字符串处理
arr.sort(function (num1, num2){
	return num1-num2;
});

alert(arr);
```

```
var arr=[12,78, 335, 21, 2];

// arr.sort();	//默认的情况下，他会把所有的东西都当做字符串处理
arr.sort(function (num1, num2){
	if(num1<num2)
	{
		return -1;	//第一个比第二个靠左
	}
	else if(num1>num2)
	{
		return 1;	//右
	}
	else
	{
		return 0;	//一样，随便你
	}
});

alert(arr);
```

```
var arr=['left', 'float', 'apple', 'content', 'text', 'zoom', 'Apple', 'Zoom'];

arr.sort();

alert(arr);
```


