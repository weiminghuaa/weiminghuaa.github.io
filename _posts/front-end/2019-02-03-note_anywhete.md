---
layout: post
title: Note Anywhere 实现原理
date: 2019-02-03 21:58:35.000000000 +08:00
tags: [Learn, 前端, chrome, extension]
---

Note Anywhere是一个非常优秀的chrome插件，可以在web页面的任何地方，插入一段笔记。

Note Anywhere 的实现可以分为这几个部分：

1. options
2. background
3. popup

## background 和 content_scripts

> background

后台（姑且这么翻译吧），是一个常驻的页面，它的生命周期是插件中所有类型页面中最长的，**它随着浏览器的打开而打开，随着浏览器的关闭而关闭，所以通常把需要一直运行的、启动就运行的、全局的代码放在background里面**。

background的权限非常高，几乎可以调用所有的Chrome扩展API（除了devtools），而且它可以无限制跨域，也就是可以跨域访问任何网站而无需要求对方设置CORS。

需要特别说明的是，虽然你可以通过chrome-extension://xxx/background.html直接打开后台页，但是你打开的后台页和真正一直在后台运行的那个页面不是同一个，换句话说，你可以打开无数个background.html，但是真正在后台常驻的只有一个，而且这个你永远看不到它的界面，只能调试它的代码。

比如添加右键菜单，就适合放到background。

> content_scripts

所谓content-scripts，其实就是Chrome插件中向页面注入脚本的一种形式（虽然名为script，其实还可以包括css的），借助content-scripts我们可以实现通过配置的方式轻松向指定页面注入JS和CSS（如果需要动态注入，可以参考下文），最常见的比如：广告屏蔽、页面CSS定制，等等。

content-scripts和原始页面共享DOM，但是不共享JS，如要访问页面JS（例如某个JS变量），只能通过injected js来实现

### manifest配置

```js
// 会一直常驻的后台JS或后台页面
"background":
{
    // 2种指定方式，如果指定JS，那么会自动生成一个背景页
    "page": "background.html"
    //"scripts": ["js/background.js"]
},

"content_scripts": [{
  "css": [ "asset/stickies.css" ],
  "js": [ "asset/stickies.js" ],
  // "<all_urls>" 表示匹配所有地址
  // "matches": ["<all_urls>"],	
  "matches": [ "http://*/*", "https://*/*" ],
	// 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle
  "run_at": "document_end"
}],
```

```html
<html>
    <head>
        <script src="asset/background.js"></script>
    </head>
</html>
```

### browserAction

> 1. background.js: 响应browserAction，创建note

```js
chrome.browserAction.onClicked.addListener(function(tab) {
	if(!skipUrl(tab.url,true))
		newNote();
});

var newNote =  function() {
	id = ++highestId;
	code = 'newNote('+id+')';
  chrome.tabs.getSelected(null, function(tab) {
      chrome.tabs.executeScript(tab.id, {code: code}); // 调用的是stickies.js的newNote方法
  });
};
```

> 2. 调用的是stickies.js的newNote方法

```js
function newNote(id)
{
    var note = new Note();
    note.id = id;
    note.timestamp = new Date().getTime();
    note.left = (window.pageXOffset + Math.round(Math.random() * (window.innerWidth - 150))) + 'px';
    note.top = (window.pageYOffset + Math.round(Math.random() * (window.innerHeight - 200))) + 'px';
    note.zIndex = ++highestZ;
    note.saveAsNew();
	notes[notes.length] = note.id;
	chrome.extension.sendRequest({command:"updateCount",data:notes.length});
	note.editField.focus();
}
```

```js
function Note()
{
	saveAsNew: function()
  {
    this.timestamp = new Date().getTime();
    var note = this;
		chrome.extension.sendRequest({command:"saveAsNew",data:{id:note.id, text:note.text, timestamp:note.timestamp, left:note.left, top:note.top, zindex:note.zIndex, url:window.location.href}},function(response){console.log(response.message+response.id);});
  },	
}
```

### 打开页面即加载旧笔记数据的实现

打开页面即可加载之前的笔记，在background.js实现

```js
chrome.tabs.onCreated.addListener(function(tab) {
	if(!skipUrl(tab.url)){
		loadCSS();
		loadNotes();
	}
	updateCount(tab);
});

chrome.tabs.onUpdated.addListener(function(tabId,changeInfo,tab) {
	if(!skipUrl(tab.url)){
		loadCSS();
		loadNotes();
	}
	updateCount(tab);
});

var loadCSS = function(){
	code = 'loadCSS('+JSON.stringify(localStorage)+')';
	chrome.tabs.getSelected(null, function(tab) {
		chrome.tabs.executeScript(tab.id, {code: code});
	});
}

var loadNotes = function(){
	chrome.tabs.getSelected(null, function(tab) {
		db.transaction(function(tx) {
			tx.executeSql("SELECT * FROM WebKitStickyNotes WHERE url = ?", [tab.url], function(tx, result) {
				var data =[];
				for (var i = 0; i < result.rows.length; ++i){
					data[i] = result.rows.item(i);
				}
				code = 'loadNotes('+JSON.stringify(data)+')';
				chrome.tabs.executeScript(tab.id, {code: code});
			}, function(tx, error) {
				alert('Failed to retrieve notes from database - ' + error.message);
				return;
			});
		});
	});
}
```

具体的加载在stickies.js

```js
function loadCSS(json){
	localstorage = eval(json);
	applyCSS(localstorage);
}

function loadNotes(data)
{
	data = eval(data);
	for (var i = 0; i < data.length; ++i) {
		var row = data[i];
		if(notes.indexOf(row.id) == -1){
			var note = new Note();
			note.id = row.id;
			note.text = row.note;
			note.timestamp = row.timestamp;
			note.left = row.left;
			note.top = row.top;
			note.zIndex = row.zindex;
			if(note.zIndex == ''){
				note.zIndex = highestZ;
			}

			if (row.zindex > highestZ)
				highestZ = row['zindex'];
			notes[notes.length] = note.id;
		}
	}
}
```

### 数据

所有的数据是使用数据库，实现增删改查，所有操作定义在background.js

#### 创建数据库和数据表

```js
try {
	if (window.openDatabase) {
		db = openDatabase("NoteAnyWhereV1", "1.0", "Note AnyWeb AnyWhere", 2000000);
		if (!db)
			alert("Failed to open the database on disk.  This is probably because the version was bad or there is not enough space left in this domain's quota");
		else{
			 db.transaction(function(tx) {
				tx.executeSql('CREATE TABLE IF NOT EXISTS WebKitStickyNotes (id REAL UNIQUE, note TEXT, timestamp REAL, left TEXT, top TEXT, zindex REAL, url TEXT)', 
				  []);
			  });
		}
	} else
		alert("Couldn't open the database.  Please try with a WebKit nightly with this feature enabled");
}catch(err) {}
```

#### 数据库操作：chrome.extension.sendRequest

> 大部分地方通过chrome.extension.sendRequest查数据

```js
chrome.extension.sendRequest({command:"saveAsNew",data:{id:note.id, text:note.text, timestamp:note.timestamp, left:note.left, top:note.top, zindex:note.zIndex, url:window.location.href}},function(response){console.log(response.message+response.id);});
```

> background.js定义chrome.extension.onRequest可处理的command

```js
chrome.extension.onRequest.addListener(
	function(request,sender,sendResponse) {
		if(request.command == 'save'){
			note = request.data;
			db.transaction(function (tx){
				tx.executeSql("UPDATE WebKitStickyNotes SET note = ?, timestamp = ?, left = ?, top = ?, zindex = ?, url = ? WHERE id = ?", [note.text, note.timestamp, note.left, note.top, note.zindex, note.url, note.id]);
			});
			sendResponse({message:"Saved",id:request.data.id});
		}else if(request.command == 'saveAsNew'){
			note = request.data;
			db.transaction(function (tx) {
				tx.executeSql("INSERT INTO WebKitStickyNotes (id, note, timestamp, left, top, zindex, url) VALUES (?, ?, ?, ?, ?, ?,?)", [note.id, note.text, note.timestamp, note.left, note.top, note.zindex, note.url]);
			});
			sendResponse({message:"SavedNew",id:request.data.id});
		}else if(request.command == 'close'){
			db.transaction(function(tx) {
				tx.executeSql("DELETE FROM WebKitStickyNotes WHERE id = ?", [request.data.id]);
			});
			sendResponse({message:"Deleted",id:request.data.id});
		}else if(request.command == 'updateCount'){
			chrome.tabs.getSelected(null, function(tab) {
				updateCount(tab,request.data);
			});
		}else if(request.command == 'summary'){
			getSummary(request.data.page);
			window.notesumtimer = setInterval(function(){
				if(window.notesum !== null){
					sendResponse({message:"Summary",summary:window.notesum});
					window.clearInterval(window.notesumtimer);
				}
			},2);
			
		}
	}
);
```

#### 保存note

两个事件用来保存note：onKeyUp、onMouseUp，在stickies.js实现

```js

Note.prototype = {

	onKeyUp: function()
  {
    this.dirty = true;
    this.saveSoon();
  },

	onMouseUp: function(e)
  {
    document.removeEventListener('mousemove', this.mouseMoveHandler, true);
    document.removeEventListener('mouseup', this.mouseUpHandler, true);

    this.save();
    return false;
  },

	saveSoon: function()
  {
    this.cancelPendingSave();
    var self = this;
    this._saveTimer = setTimeout(function() { self.save() }, 200);
  },

	save: function()
	{
  	this.cancelPendingSave();

  	if ("dirty" in this) {
    	this.timestamp = new Date().getTime();
    	delete this.dirty;
  	}

  	var note = this;
		chrome.extension.sendRequest({command:"save",data:{id:note.id, text:note.text, timestamp:note.timestamp, left:note.left, top:note.top, zindex:note.zIndex, url:window.location.href}},function(response){console.log(response.message+response.id);});
	},
```

## popup

popup是点击browser_action或者page_action图标时打开的一个小窗口网页，焦点离开网页就立即关闭，一般用来做一些临时性的交互。

popup可以包含任意你想要的HTML内容，并且会自适应大小。可以通过default_popup字段来指定popup页面，也可以调用setPopup()方法。

配置方式：

```js
{
  "browser_action":
  {
    "default_icon": "img/icon.png",
    // 图标悬停时的标题，可选
    "default_title": "这是一个示例Chrome插件",
    "default_popup": "popup.html"
  }
}
```

需要特别注意的是，由于单击图标打开popup，焦点离开又立即关闭，所以popup页面的生命周期一般很短，需要长时间运行的代码千万不要写在popup里面。

> 本项目并没显示的配置popup，而且实际使用时也没有显示popup页面。我觉得很有可能，虽然项目有popup代码，确实没有用到。没有popup挺方便的。

## options

options是功能配置模块。除了配置note的字体颜色等，还另外提供了可以查看所有笔记的功能，非常优秀。

### options.html

非常简单，其中笔记的背景是使用div加上background color实现的，没有用图片。

有2个功能：Notes Summary、Settings，点击后，调用stickies_box.js的getSummaryBox和getSettingBox两个函数，最后显示Notes Summary、Settings在页面上。**所以，主要的功能是stickies_box.js实现的**。options.htmldom结构如下：（这3个div的层级，貌似并不灵活，可能应该使用flex会好很多）

```html
<body>
	<div id="container">
        <div id="header">Note Anywhere</div>
		<div class="main">
            // 功能 div
            <div id="dashboard" class="note-anywhere" style="margin-top:15px;">
            </div>

            // 好像没有用
            <div id="preview" class="note-anywhere" style="margin-top:260px;display:none;">
            </div
        </div>
	</div>

    // 这样好像没有用到
	<div id="settingsholder" style="display:none;">
		
	</div>

    // Notes Summary div
    <div class="note-anywhere big" style="left: 200px; top: 57px; z-index: 1;">
    </div>

    // Settings div
    <div class="note-anywhere big" style="left: 513px; top: 57px; z-index: 2;">
    </div>
</body>
```

**ps: 可以像开发普通web一样，先不管插件那块，单独调试web页面**

### stickies_box.js

#### getSettingBox

> 第一步：调用updatePos();

作用：获取坐标位置

```js
function updatePos(){
	d_pos = $('#dashboard').position(); // Object {top: 42, left: 0}
	l_pos = {left:d_pos.left+170+30,top:d_pos.top+15}; // {left: 200, top: 57}
	r_pos = {left:d_pos.left+170+283+60,top:d_pos.top+15}; // {left: 513, top: 57}
}
```

> 第二步：判断是否有SummaryBox，得到最终settings面板应该显示的pos，是使用l_pos，还是r_pos

```js
    if(boxes[summaryboxid]){
		if(boxes[summaryboxid].left == l_pos.left+'px')
			pos = r_pos;
		else
			pos = l_pos;
	}else
			pos = l_pos;
```

js还有这种变态的用法，可以不声明直接用，卧槽
```js
http://www.imooc.com/qadetail/78057

Javascript声明变量的时候，虽然用var关键字声明和不用关键字声明，很多时候运行并没有问题，但是这两种方式还是有区别的。可以正常运行的代码并不代表是合适的代码。

JS中变量申明分显式申明和隐式申明。

Var i=100  显示申明

i=100  隐式申明

在函数中使用var关键字进行显式申明的变量是做为局部变量，而没有用var关键字，使用直接赋值方式声明的是全局变量。

当我们使用访问一个没有声明的变量时，JS会报错。而当我们给一个没有声明的变量赋值时，JS不会报错，相反它会认为我们是要隐式申明一个全局变量，这一点一定要注意。

var num = 1是在当前域中声明变量. 如果在方法中声明，则为局部变量；如果是在全局域中声明，则为全局变量。而num = 1，事实上是对属性赋值操作。首先，它会尝试在当前作用域链（如在方法中声明，则当前作用域链代表全局作用域和方法局部作用域etc。。。）中解析 num； 如果在任何当前作用域链中找到num，则会执行对num属性赋值； 如果没有找到num，它才会在全局对象（即当前作用域链的最顶层对象，如window对象）中创造num属性并赋值。

注意！它并不是声明了一个全局变量，而是创建了一个全局对象的属性。
```

比如没有的话，最终使用l_pos：

```html
<div class="note-anywhere big" style="left: 200px; top: 57px; z-index: 1;"></div>
```

> 第三步：判断是否有SettingBox, 没有就创建，有的话更新pos

```js
if(!boxes[settingboxid]){
		settingboxid = newNoteBox('settings',pos);
		$(boxes[settingboxid].editField).append(getSettingForm());
		load_options();
		jscolor.init();
	}else{
		boxes[settingboxid].pos = pos;
		boxes[settingboxid].promote();
	}
```

```html
<div class="note-anywhere big" style="left: 200px; top: 57px; z-index: 1;">
	<div class="closebutton"></div>
	<div class="edit">
		<form>
			// 设置面板, 由getSettingForm()方法生成
		</form>
	</div>
	<div class="timestamp">settings</div>
</div>
```

##### 创建box函数newNoteBox

```js
function newNoteBox(stamp,pos)
{
  var note = new NoteBox(); // 传进NoteBox

  note.id = ++id; // 设置id

	if(!stamp){ // stamp为空，设置右下角的印花为时间
		note.timestamp = new Date().getTime();
	}else // 设置印花
		note.stamp = stamp;

	// 设置位置
  note.left = pos.left + 'px';
  note.top = pos.top + 'px';
  note.zIndex = ++highestZ;
	
	boxes[note.id] = note;
	return note.id;
}
```

NoteBox就是note容器，创建完的dom：

```html
<div class="note-anywhere big" style="left: 200px; top: 57px; z-index: 1;">
	<div class="closebutton"></div>
	<div class="edit">
		// 内容，对于设置和notes summary，内容是stickies_box.js做好的，对于用户的笔记，则不是
	</div>
	<div class="timestamp">settings</div>
</div>
```


#### getSummaryBox

与getSettingBox流程一致，不同的是这里需要调用 updateSummaryBox(1) 显示已有的用户做的笔记

> updateSummaryBox

```js
function updateSummaryBox(page){
	// 查询summary list数据
	chrome.extension.sendRequest({command:"summary",data:{page:page}},function(response){
		// response: {message: "Summary", summary: Array(1)}
		// response.summary: [{count: 1, url: "https://weibo.com/xilie/home?wvr=5"}]
		eles = sum2HTML(response.summary,page);
		$(boxes[summaryboxid].editField).empty();
		// 遍历eles，插入editField
		$.each(eles,function(i,x){$(boxes[summaryboxid].editField).append(x)});
	});
}
```

> sum2HTML

将sumary转成html

结果：

```html
// summary list
<ul class="sum-list">
	<li>
		<div class="cbox">
			<span>1</span>
		</div>
		<div class="abox">
			<a href="https://weibo.com/xilie/home?wvr=5" target="_blank">https://weibo.com/xilie/home?wvr=5</a>
		</div>
	</li>
</ul>
// 翻页按钮和刷新按钮
<div class="bbox">
	<a href="###">
		<img src="asset/refresh.png">
	</a>
</div>
```
