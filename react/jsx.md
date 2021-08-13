## 写在前面

本系列是博主在学习 React 过程中 阅读如下文档做出的笔记 如果有错误 希望在评论区指出哦 🤩🤩🤩

> <a href="https://react.docschina.org/docs/getting-started.html">React 官网</a>

> <a href="https://www.bookstack.cn/read/react-naive-book/about.md">React.js 小书</a>

> <a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg5MDAzNzkwNA==&action=getalbum&album_id=1566025152667107329&scene=173&from_msgid=2247483922&from_itemidx=1&count=3#wechat_redirect">React 系列教程</a>

> <a href="https://www.redux.org.cn/">Redux 中文文档</a>

> <a href="https://cn.mobx.js.org/">Mobx 中文文档</a>

> <a href="https://react-guide.github.io/react-router-cn/index.html">REACT ROUTER 中文文档</a>

> <a href="https://reactrouter.com/web/guides/quick-start">REACT ROUTER</a>

> <a href="https://www.nextjs.cn/">Next.js</a>

预计将会更新如下内容

- React 小册 - 起步 JSX ✅ ✅

- React 小册 - 扬帆起航

- React 小册 - Hooks

- React 小册 - CSS 解决方案

- React 小册 - 生命周期

- React 小册 - 状态管理 Redux

- React 小册 - 状态管理 Redux 中间件

- React 小册 - 状态管理 Mobx

- React 小册 - Router

- React 小册 - SSR

- React 小册 - React 生态

> 本系列的所有代码你都可以在这个 👉<a href="https://github.com/LuckyChou710/ReStart-FE/tree/main/react/react-vite">仓库</a> 中看到 -->

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/xmind/React.png"/>

## 你可能需要知道的一丢丢 JS 语法

### 解构

```js
const target = {
  name: 'nanshu',
  desc: {
    height: 181,
    age: 18,
  },
};
// 解构第一层
const { name } = target;

// 解构第二层
const {
  desc: { age },
} = target;
```

### 展开运算符

展开运算符可以帮我们快速的浅拷贝一个可迭代的对象

例如我们要往一个对象中添加属性 或者覆盖属性时 我们不希望改变原来的对象 就可以使用这个

当然 展开运算符的使用场景 不仅于此 📝

```js
const obj = { nickname: 'nanshu' };
const newObj = { ...obj, age: 18 };
```

### 箭头函数

箭头函数表达式的语法比函数表达式更简洁，并且没有自己的 this 🥳

当我们需要返回一个对象时 可以用()包裹对象 从而省略 return

```js
const setUsername = (username: string) => ({
  type: 'SET_USER_NAME',
  username,
});
```

### ESM

| 描述       | 语法                         | 说明                            |
| :--------- | :--------------------------- | :------------------------------ |
| 模块导出   | export xxx                   |                                 |
| 默认导出   | export default xxx           | 一个模块只能有一个默认导出      |
| 重新导出   | export \* from xxx           | 常作为聚合导出 统一管理模块入口 |
| 重命名导出 | export {xxx as xxx} from xxx |                                 |
| 默认导入   | import xxx from xxx          | 一个模块只能有一个默认导入      |
| 解构导入   | import {xxx} from xxx        |                                 |
| 重命名导入 | import {xxx as xxx} from xxx |                                 |

## 起步

### 在 html 中使用 React

在 html 使用 React 我们需要引入如下 script

- `<script crossorigin src="https://unpkg.com/react@17/umd/react.development.js"></script>`

- `<script crossorigin src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>`

  - 将组件挂载到根节点上进行渲染

- `<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>`

  - 将 JSX 语法转换为 React.createElement

> **注意：在任何你使用到 JSX 语法的 script 内 加上 type="text/babel" 否则它将不会生效**

例如如下代码 你就可以在页面上看到 Hello React

```html
<body>
  <div id="root"></div>
  <script
    crossorigin
    src="https://unpkg.com/react@17/umd/react.development.js"
  ></script>
  <script
    crossorigin
    src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"
  ></script>
  <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

  <script type="text/babel">
    const App = () => <h1>Hello React</h1>;
    ReactDOM.render(<App />, document.getElementById('root'));
  </script>
</body>
```

## JSX

### JSX 语法本质

JSX 是 JS 的语法扩展 不能被浏览器直接执行 需要使用 babel 编译后 才可以在浏览器中正常执行

JSX 在 React 中用来描述用户界面 最终被 babel 编译为`React.createElement(type,attrs,children)`

- type：用于标识节点的类型。它可以是类似“h1”“div”这样的标准 HTML 标签字符串，也可以是 React 组件类型或 React fragment 类型。

- config：以对象形式传入，组件所有的属性都会以键值对的形式存储在 config 对象中。

- children：以对象形式传入，它记录的是组件标签之间嵌套的内容，也就是所谓的“子节点”“子元素”。

所以 JSX 本质是只是一种语法糖 最终还是被转化为了`React.createElement`

我们可以在[babel](https://www.babeljs.cn/)官网中 查看 JSX 最终被编译成了什么

```js
<div class="header">
  <div
    class="title"
    onClick={() => {
      console.log('hello');
    }}
  >
    Title
  </div>
</div>;

// 经babel转换后
('use strict');

/*#__PURE__*/
React.createElement(
  'div',
  {
    class: 'header',
  },
  /*#__PURE__*/ React.createElement(
    'div',
    {
      class: 'title',
      onClick: () => {
        console.log('hello');
      },
    },
    'Title'
  )
);
```

<hr />

createElement 中并没有十分复杂的涉及算法或真实 DOM 的逻辑，它的每一个步骤几乎都是在格式化数据

也就是处理成我们耳熟能详的`虚拟DOM`，关于`虚拟DOM` 我们都知道它解决了大量 DOM 操作带来的性能问题

但是 时至今日 浏览器的性能已经完成可以忽视这方面的消耗 `虚拟DOM` 在我看来更是一种一统大前端的野心

因为`虚拟DOM`中包含着描述 UI 的信息 如果有一套规则可以将这些信息转换成每个平台支持的语言 那么 React 就可以屏蔽每个平台的差异 做到跨段开发

React 在早期的时候 `react` 和 `react-dom` 这两个包是在一起的 从 React15 开始 才开始分离 各司其职

这也是 RN 诞生的时候 这也可见一斑 `react-dom` 的作用

<hr />

至此 `createElement` 完成了它的任务 那就是格式化数据

接下来 将由`ReactDOM.render` 接入处理好的数据 然后将它挂在真正的 dom 节点上

至此 你的页面便出现了

```js
ReactDOM.render(
  // 需要渲染的元素（ReactElement）
  element,
  // 元素挂载的目标容器（一个真实DOM）
  container,
  // 回调函数，可选参数，可以用来处理渲染结束后的逻辑
  [callback]
);
```

### JSX 表达式

JSX 允许 在`{}`中使用我们的 JS 表达式

在里面 我们可以使用变量 进行运算符的操作 调用方法 等等

```jsx
<div>{new Date().toDateString()}</div>
```

### JSX 注释

JSX 有特殊的注释方法

```js
{
  /* 我是注释  这里是一个头部信息*/
}
<div className="header">我是头部</div>;
```

### 特殊点

- 由于 JSX 是 JS 的语法扩展 但是在里面我们又可以书写类似 HTML 的东西 所以为了避免冲突

  - HTML 中 class 在 JS 中表示类 在 JSX 中 我们用**className**来代替

  - HTML 中 label 标签的 for 在 JS 中表示循环 在 JSX 中用**htmlFor**来代替

- undefined / boolean / null

  - 对于以上几种数据类型 JSX 不会将其渲染到页面上

  - 利用这个特性 我们可以实现条件渲染

```javascript
let flag = true
<div>{flag && "欢迎回来~~~"}</div>
```

### JSX 列表渲染

这一块主要使用到了数组的高阶函数 map

```jsx
const _games = ['剑盾', '塞尔达', '马里奥制造', '风花雪月'];

const gamesUI = _games.map((item, index) => {
  // 🙅‍♂️避免使用index作为唯一的key去使用 一般后端返回的数据都有唯一的key值
  return <div key={index}>{item}</div>;
});
```

### JSX 事件绑定

例如 我们为如下的 dom 节点绑定一个点击事件

```jsx
const handleClick = () => {
  console.log('click...');
};

<button onClick={handleClick}>Click</button>;
```

如果在类组件中 你可能就需要考虑到 this 的绑定问题了

使用 bind ｜｜ 箭头函数 😛😛😛

## 开发规范

### 文件目录

| 内容                   | 路径           | 说明                                                                         |
| :--------------------- | :------------- | :--------------------------------------------------------------------------- |
| 存放静态资源，例如图片 | src/assets     | 根据模块可以在 assets 下再新建文件夹                                         |
| 业务页面               | src/containers | 对应需求事实际页面                                                           |
| 业务组件               | src/components | 业务公共组件，有些组件在封装的时候可以考虑后期的可扩展性，及通用性           |
| 页面路径               | src/routes     | 页面路径                                                                     |
| 业务公共样式           | src/styles     | 全局公共样式，可以在其他地方引用                                             |
| 实用公共函数           | src/utils      | 例如网络请求 request，公共函数等                                             |
| redux 状态             | src/store      | 根据模块在划分文件                                                           |
| 业务封装的钩子函数     | src/hooks      | 与 components 思路类似，有些组件如果业务中多个地方在用，可以考虑后期抽离出来 |
| 服务端接口             | src/api        | 存放所有的服务端接口                                                         |
| mock 数据              | src/mock       | 存放一些 mock 数据                                                           |
