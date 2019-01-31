---
layout: post
title: Native-JS-Bridge
date: 2019-01-19 15:30:35.000000000 +08:00
tags: ios
---

[从零收拾一个hybrid框架（一）-- 从选择JS通信方案开始](http://awhisper.github.io/2018/01/02/hybrid-jscomunication/)

## 几种JS Native相互通信方式的介绍

### JS 调用 Native 的几种通信方案

#### 假跳转的请求拦截

- 安卓的拦截方式 shouldOverrideUrlLoading

- iOS的UIWebView的拦截方式 webView:shouldStartLoadWithRequest:navigationType:

- iOS的WKWebView的拦截方式 webView:decidePolicyForNavigationAction:decisionHandler:


#### 弹窗拦截

alert()
prompt()
confirm()

- 安卓的拦截 onJsPrompt（其他的两个弹框也有）

- iOS的WKWebView webView:runJavaScriptTextInputPanelWithPrompt:balbala

- iOS的UIWebView UIWebView不支持截获任何一种弹框，因此这条路走不通(私有API有)

#### JS上下文注入

- 苹果JavaScriptCore注入

```Objective-C
//拿到当前WebView的JS上下文
JSContext *context = [webview valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
//给这个上下文注入callNativeFunction函数当做JS对象
context[@"callNativeFunction"] = ^( JSValue * data )
{
    //1 解读JS传过来的JSValue  data数据
    //2 取出指令参数，确认要发起的native调用的指令是什么
    //3 取出数据参数，拿到JS传过来的数据
    //4 根据指令调用对应的native方法，传递数据
    //5 此时还可以将客户端的数据同步返回！
}
```

```js
//准备要传给native的数据，包括指令，数据，回调等
var data = {
    action:'xxxx',
    params:'xxxx',
    callback:'xxxx',
};
//直接使用这个客户端注入的函数
callNativeFunction(data);
```

TIPS:
这种注入不止可以把block注入，在JS里成为一个JS函数，还可以把字符/数字/字典等数据直接注入到JS全局对象之中，可以让JS访问到Native才能获取的全局对象，甚至还可以注入任何NSObject对象，只要这个NSObject对象遵循JSExportOC的协议，相当于JS可以直接调用访问OC的内存对象


- 安卓addJavascriptInterface注入

```java
// 通过addJavascriptInterface()将Java对象映射到JS对象
//参数1：Javascript对象名
//参数2：Java对象名
mWebView.addJavascriptInterface(new AndroidtoJs(), "nativeObject");
```
```js
nativeObject.callFunction("js调用了android中的hello方法");
```

- 苹果scriptMessageHandler注入

```Objective-C
//配置对象注入
[self.webView.configuration.userContentController addScriptMessageHandler:self name:@"nativeObject"];
//移除对象注入
[self.webView.configuration.userContentController removeScriptMessageHandlerForName:@"nativeObject"];
```

```js
window.webkit.messageHandlers.nativeObject
```

并且和之前的两种注入也不同，前两种注入都可以让js任意操作所注入自定义对象的所有方法，而addScriptMessageHandler注入其实只给注入对象起了一个名字nativeObject，但这个对象的能力是不能任意指定的，**只有一个函数postMessage**，因此JS的调用方式也只能是

```js
//准备要传给native的数据，包括指令，数据，回调等
var data = {
    action:'xxxx',
    params:'xxxx',
    callback:'xxxx',
};
//传递给客户端
window.webkit.messageHandlers.nativeObject.postMessage(data);
```

## Native 调用 JS 的几种通信方案

### JS是一个脚本语言，在设计之初就被设计的任何时候都可以执行一段字符串js代码，换句话说，任何一个js引擎都是可以在任意时机直接执行任意的JS代码，我们可以把任何Native想要传递的消息/数据直接写进JS代码里，这样就能传递给JS了

evaluatingJavaScript 直接注入执行JS代码

```js
function calljs(data){
    console.log(JSON.parse(data)) 
    //1 识别客户端传来的数据
    //2 对数据进行分析，从而调用或执行其他逻辑  
}
```

```Objective-C
//不展开了,data是一个字典，把字典序列化
NSString *paramsString = [self _serializeMessageData:data];
NSString* javascriptCommand = [NSString stringWithFormat:@"calljs('%@');", paramsString];// calljs('{data:xxx,data2:xxx}');
//要求必须在主线程执行JS
if ([[NSThread currentThread] isMainThread]) {
    [self.webView evaluateJavaScript:javascriptCommand completionHandler:nil];
} else {
    __strong typeof(self)strongSelf = self;
    dispatch_sync(dispatch_get_main_queue(), ^{
        [strongSelf.webView evaluateJavaScript:javascriptCommand completionHandler:nil];
    });
}
```

### loadUrl 执行JS代码

安卓在4.4以前是不能用evaluatingJavaScript这个方法的，因此之前安卓都用的是webview直接loadUrl，但是传入的url并不是一个链接，而是以”javascript:”开头的js代码，从而达到让webview执行js代码的作用

```java
mWebView.loadUrl("javascript:callJS(\'{data:xxx,data2:xxx}\')");
```

### WKUserScript 执行JS代码

WKWebView官方提供了一个Api，可以让WebView在加载页面的时候，自动执行注入一些预先准备好的JS

```Objective-C
//在loadurl之前使用
//time是一个时机参数，可选dom开始加载/dom加载完毕，2个时机进行执行JS
//构建userscript
WKUserScript *script = [[WKUserScript alloc]initWithSource:source injectionTime:time forMainFrameOnly:mainOnly];
WKUserContentController *userController = webView.userContentController;
//配置userscript
[userController addUserScript:script]
```

## 几种通信方式的优缺点对比

### 假请求的通信拦截的问题 – 当下最不该选择的通信方式

优点：
1. 版本兼容性好：iOS6及以前只有这唯一的一种方式
2. webview支持度好：简单地说框架的开发者容易偷懒

缺点：
1. 丢消息！ 一个通信方案，结果他最大的问题是丢失通信消息！
2. URL长度限制

### 弹窗拦截

1. UIWebView不支持
2. 安卓一切正常，不会出现丢消息的情况
3. WKWebView一切正常，也不会出现丢消息的情况，但其实WKWebView苹果给了更好的API

优点：弹窗拦截也可以支持同步返回！

prompt( ) 拦截在客户端需要执行confirm(data)从而用同步的方式给客户端返回数据到JS

```js
//同步JS调用Native  JS这边可以直接写 =  !!!
var nativeNetStatus = nativeObject.getNetStatus();
//异步JS调用Native JS只能这么写
nativeObject.getNetSatus(callback(net){
    console.log(net)
})
```

> 唯一需要注意的一点，如果你的业务开发中经常希望在前端代码里使用系统alert()/confirm()/prompt()那么，你还是挑一个不常用的进行hook，以免干扰常规业务

总体应该用的人很少

### JS上下文注入

#### UIWebView的JSContext注入

> 如果你还在使用UIWebView，就用JSContext吧！不要犹豫!

但是应该很少人还在用UIWebview了吧，放弃

#### 安卓的addJavascriptInterface注入

略

#### WKWebView的scriptMessageHandler注入

优点:

1. 无需json化传递数据
是的，webkit.messageHandlers.xxx.postMessage()是支持直接传递json数据，无需前端客户端字符串处理的

2. 不会丢消息

缺点：

1. 版本要求iOS8

2. 不支持JSContext那样的同步返回

## 总结

看下对比：http://awhisper.github.io/2018/01/02/hybrid-jscomunication/

最终，我认为：

1. 现在用的`WebViewJavascriptBridge`，虽然是假跳转，但是用的挺好的，只是看起来不那么高大上，另外，刻意改成js注入，还要改sdk，就不改好了。

2. js注入应该是标准方案，新项目，应该考虑这种方案，向日葵保险的安卓项目，应该用这个方案实现的

