# JavaScript 全栈工程师培训教程--学习笔记(2-React 技术栈）

wmh: 前端网页用React，手机端用React-Native。爽！

## JSX 语法

React 使用 JSX 语法，JavaScript 代码中可以写 HTML 代码。

```javascript
let myTitle = <h1>Hello, world!</h1>;
```

wmh: 确实方便了。

---

## Babel 转码器

JavaScript 引擎（包括浏览器和 Node）都不认识 JSX，需要首先使用 Babel 转码，然后才能运行。

```html
<script src="react.js"></script>
<script src="react-dom.js"></script>
<script src="babel.min.js"></script>
<script type="text/babel">
  // ** Our code goes here! **
</script>
```

React 需要加载两个库：React 和 React-DOM，前者是 React 的核心库，后者是 React 的 DOM 适配库。

Babel 用来在浏览器转换 JSX 语法，如果服务器已经转好了，浏览器就不需要加载这个库。

---

## 示例：React 组件

React 允许用户定义自己的组件，插入网页。

浏览器打开`demos/react-component-demo/index1.html`，按照[《操作说明》](../demos/README.md#react-组件语法)，仔细查看源码。

```javascript
class MyTitle extends React.Component {
  render() {
    return <h1>Hello World</h1>;
  }
};

ReactDOM.render(
  <MyTitle/>,
  document.getElementById('example')
);
```

ReactDOM.render方法接受两个参数：一个虚拟 DOM 节点和一个真实 DOM 节点，作用是将虚拟 DOM 挂载到真实 DOM。

---

## 组件的生命周期

React 为组件的不同生命阶段，提供了近十个钩子方法。

- `componentWillMount()`：组件加载前调用
- `componentDidMount()`：组件加载后调用
- `componentWillUpdate()`: 组件更新前调用
- `componentDidUpdate()`: 组件更新后调用
- `componentWillUnmount()`：组件卸载前调用

我们可以利用这些钩子，自动完成一些操作。

---

## React 组件库

React 的一大优势，就是网上有很多已经写好的组件库，可以使用。

React-Bootstrap：https://react-bootstrap.github.io/

---

## React 的核心思想

View 是 State 的输出。

```javascript
view = f(state)
```

只要 State 发生变化，View 也要随之变化。

---

React 的本质是将图形界面（GUI）函数化。

```javascript
const person = {
  name: "michel",
  age: 31
}

const App = ({ person }) => <h1>{ person.name }</h1>

ReactDOM.render(<App person={person} />, document.body)
```

---

## React 应用的架构

React 只是一个 DOM 的抽象层，并没有解决应用程序的架构问题：大型应用程序应该如何组织代码？

Facebook 提出 Flux 架构的概念。

最大特点：数据单向流动

---

## 目前最流行的两个 React 框架

- MobX：采用观察者模式，自动响应数据变化
- Redux：Flux 的函数式实现
