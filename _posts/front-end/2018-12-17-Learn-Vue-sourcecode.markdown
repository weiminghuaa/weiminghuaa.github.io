---
layout: post
title: Vue学习-源码分析
date: 2018-12-17 21:58:35.000000000 +08:00
tags: [Learn, 前端, Vue, 源码]
---

[Vue技术内幕](http://hcysun.me/vue-design/)
[Vue.js 技术揭秘](https://ustbhuangyi.github.io/vue-analysis/)

## Vue.js 源码目录设计

Vue.js 的源码都在 src 目录下，其目录结构如下。

```
src
├── compiler        # 编译相关 
├── core            # 核心代码 
├── platforms       # 不同平台的支持
├── server          # 服务端渲染
├── sfc             # .vue 文件解析
├── shared          # 共享代码
```

> sfc: single-file component

###compiler
compiler 目录包含 Vue.js 所有编译相关的代码。它包括把模板解析成 ast 语法树，ast 语法树优化，代码生成等功能。

编译的工作可以在构建时做（借助 webpack、vue-loader 等辅助插件）；也可以在运行时做，使用包含构建功能的 Vue.js。显然，编译是一项耗性能的工作，所以更推荐前者——离线编译。

### core
core 目录包含了 Vue.js 的核心代码，包括内置组件、全局 API 封装，Vue 实例化、观察者、虚拟 DOM、工具函数等等。

这里的代码可谓是 Vue.js 的灵魂，也是我们之后需要重点分析的地方。

### platform
Vue.js 是一个跨平台的 MVVM 框架，它可以跑在 web 上，也可以配合 weex 跑在 native 客户端上。platform 是 Vue.js 的入口，2 个目录代表 2 个主要入口，分别打包成运行在 web 上和 weex 上的 Vue.js。

我们会重点分析 web 入口打包后的 Vue.js，对于 weex 入口打包的 Vue.js，感兴趣的同学可以自行研究。

### server
Vue.js 2.0 支持了服务端渲染，所有服务端渲染相关的逻辑都在这个目录下。注意：这部分代码是跑在服务端的 Node.js，不要和跑在浏览器端的 Vue.js 混为一谈。

服务端渲染主要的工作是把组件渲染为服务器端的 HTML 字符串，将它们直接发送到浏览器，最后将静态标记"混合"为客户端上完全交互的应用程序。

### sfc
通常我们开发 Vue.js 都会借助 webpack 构建， 然后通过 .vue 单文件来编写组件。

这个目录下的代码逻辑会把 .vue 文件内容解析成一个 JavaScript 的对象。

### shared
Vue.js 会定义一些工具方法，这里定义的工具方法都是会被浏览器端的 Vue.js 和服务端的 Vue.js 所共享的。

### 总结
从 Vue.js 的目录设计可以看到，作者把功能模块拆分的非常清楚，相关的逻辑放在一个独立的目录下维护，并且把复用的代码也抽成一个独立目录。

这样的目录设计让代码的阅读性和可维护性都变强，是非常值得学习和推敲的。

## Vue 构造函数

1. 很详细的分析了Vue的构造函数，其中，构造函数中的this，指向的是new出来的Vue的实例，类似java中的构造函数中的this

```javascript
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}
```

1. initGlobalAPI(Vue)先于Vue的构造函数执行，是否类似于java的类加载？从chrome的断点来看，是src/platforms/web/runtime/index.js的最后一行代码```export default Vue```, 导致src/core/index.js执行```initGlobalAPI(Vue)```

## 以一个例子为线索

1. window.performance: 用于调试js性能，打开 chrome 开发者工具即可查看结果[全新Chrome Devtool Performance使用指南](https://zhuanlan.zhihu.com/p/29879682)

## Vue 选项的规范化

1. js的export和import的前因后果：[Module 的语法](http://es6.ruanyifeng.com/#docs/module)

其中，如果想在一条import语句中，同时输入默认方法和其他接口，可以写成下面这样。
```javascript
import _, { each, forEach } from 'lodash';
```
对应上面代码的export语句如下。
```javascript
export default function (obj) {
  // ···
}

export function each(obj, iterator, context) {
  // ···
}

export { each as forEach };
```

export default也可以用来输出类。
```javascript
// MyClass.js
export default class { ... }

// main.js
import MyClass from 'MyClass';
let o = new MyClass();
```

1. config.js里面的export和相关的import

```javascript
export type Config = {
  // user
  optionMergeStrategies: { [key: string]: Function };
  // ...
  // platform
  isReservedTag: (x?: string) => boolean;
  // ...
}

export default ({
  optionMergeStrategies: Object.create(null),
  // ...
  isReservedTag: no,
  // ...
}: Config)
```

> export是ES6中导出变量函数或者对象的语法。但是type并不是JS的语法了，而是属于typescript的语法了，可以这样理解：“给定义的类型起一个别名”，什么意思？

使用
```javascript
import config from '../config'

export function validateComponentName (name: string) {
  // ...
  if (isBuiltInTag(name) || config.isReservedTag(name)) {
    warn(
      'Do not use built-in or reserved HTML elements as component ' +
      'id: ' + name
    )
  }
}
```

> 这里的config的isReservedTag，到底是函数，还是属性？？？

> 虽然src/plarforms/web/runtime/index.js中，```Vue.config.isReservedTag = isReservedTag``` 将config.isReservedTag替换为函数，但是src/core里面，却都是```import config from '../config'```, core里面的代码，为什么将isReservedTag当初函数使用的呢？？？

> 怎么会乱改属性的类型呢，定义的时候是函数，为什么又改为属性呢？？？

> 破案了，```isReservedTag: no```no并不是bool值，而是src/shared/util.js定义的函数```export function noop (a?: any, b?: any, c?: any) {}```

## Vue 选项的合并

就是处理`new Vue(options)`，将options里面的各个属性处理完后，复制给vue实例对象的$option

## Vue 的初始化之开篇

懵圈了

## 揭开数据响应系统的面纱

后面看不下去了