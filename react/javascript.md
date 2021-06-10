## 你需要知道的一些 JS 知识

### 函数

函数这一块 我们主要需要掌握的就是箭头函数

因为在类组件中 我们总是不可避免的需要使用到 this 绑定

而 this 相关的操作总是或多或少的存在麻烦

但是箭头函数 有一个特性 其内部没有 this 会沿着作用域链往上级作用域查找 所以就绕开了 this 绑定的问题

简单对比一下普通函数和匿名函数

#### 普通函数

- **谁调用函数 this 就指向谁 this 的指向只有在编译期间才能被确认**
- **函数 和 变量 会自动变成全局对象 window 的方法和属性**
- **所以函数的内部的 this 指向全局对象 window**

#### 匿名函数

- **箭头函数内的 this，就是箭头函数被定义时所在作用域的 this**
- **不受 apply 等方法修改**

```javascript
const foo = () => {
  console.log(this);
};

const bar = function () {
  console.log(this);
};

const obj = {
  foo,
  bar,
};

obj.foo(); // window 在定义时就确认了
obj.bar(); // obj 在编译期发现是obj在调用bar
```

### 数组相关

数组这一块 我们需要掌握的常用 API 有

- `Array.prototype.map(item,index,array)`

  - 我们在列表渲染时 需要依赖此 API 来生成每一个结点

- `Array.prototype.slice([begin[, end]])`

  - state 中的状态 我们是不可以修改的 只能用新状态去覆盖旧状态 对于数组 我们就可以使用这个 API 来拷贝一个新状态 同理还有 concat 等也可 \(这些方法都只能深拷贝第一层\)

- 其他的高阶用法可以参考 MDN 或者我的 JS 小册\(TODO\)

### 对象相关

- 同样的对于 状态中的对象 我们也不能直接修改 可以用`Object.assign(target, ...sources)`来拷贝一个新状态\(也只能深拷贝第一层\)

- 对象字面量 写法

  - 如果对象的键值名字相同 在赋值的时候 可以只写一个

- 对象解构

```javascript
let result = {
  case1: 'A',
  case2: 'C',
};

const { case1, case2 } = result;

console.log(case1); // A
console.log(case2); // C
```

### 模块化

主要需要掌握的是 ES6 的导入/导出

```javascript
// tools.js
export function getName() {

}

export function getParam() {

}

export default function getAge() {

}

// index.js
// 一个模块中 只允许使用一个默认导出
// 默认导出的模块可以自定义名字 其他的必须与导出的名称一致
import getAge, {getName,getParam} from tools
```

### 起步

如果我们要使用 JSX 语法

我们需要在项目中引入

- `<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>`

- `<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>`

  - 将组件挂载到根节点上进行渲染

- `<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>`

  - 将 JSX 语法转换为 React.createElement

> **注意：在需要使用 babel 的 script 内 加上 style="text/babel"**

## JSX

### JSX 语法本质

JSX 是 JS 的语法扩展 不能被浏览器直接执行 需要使用 babel 编译后 才可以在浏览器中正常执行

JSX 在 React 中用来描述用户界面 最终被 babel 编译为`React.createElement(type,attrs,children)`

- type：用于标识节点的类型。它可以是类似“h1”“div”这样的标准 HTML 标签字符串，也可以是 React 组件类型或 React fragment 类型。

- config：以对象形式传入，组件所有的属性都会以键值对的形式存储在 config 对象中。

- children：以对象形式传入，它记录的是组件标签之间嵌套的内容，也就是所谓的“子节点”“子元素”。

所以 JSX 本质是只是一种语法糖 最终还是被转化为了`React.createElement`

我们可以在[babel](https://www.babeljs.cn/repl#?browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=DwEwlgbgBAxgNgQwM5ILwCIAWBTBJsBO6AfAFACQoksiKGALmPXNiQCpMvAD04EZPPsSA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.12.3&externalPlugins=)官网中 查看 JSX 最终被编译成了什么

```javascript
<div class="header">
  <div class="title">Title</div>
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
    },
    'Title'
  )
);
```

createElement 中并没有十分复杂的涉及算法或真实 DOM 的逻辑，它的每一个步骤几乎都是在格式化数据

最后将格式化后的数据交给 React.render 来处理

```javascript
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

JSX 在`{}`中使用我们的 JS 表达式

在里面 我们可以使用变量 进行运算符的操作 调用方法 等等

### JSX 注释

JSX 有特殊的注释方法

```javascript
<div className="header">
  {/* 我是注释 */}
  我是头部
</div>
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

```javascript
<script type="text/babel">

    const _games = ['剑盾', '塞尔达', '马里奥制造', '风花雪月']

    const gamesUI = _games.map((item, index) => {
        return (
            <div key={index}>{item}</div>
        )
    })

    ReactDOM.render(gamesUI, document.getElementById('root'))

</script>
```

如果在脚手架中 我们可以书写成如下

```javascript
import React, { Component } from 'react';

export class Movies extends Component {
  constructor(props) {
    super(props);
    this.state = {
      games: ['剑盾', '塞尔达', '马里奥制造', '风花雪月'],
    };
  }

  render() {
    const { games } = this.state;
    return games.map((item, index) => {
      return <div key={index}>{item}</div>;
    });
  }
}
```

### JSX 事件绑定

这一块是类组件中的重点 因为我们总是难免要使用到 this

开始吧 我们用一个计数器来开始我们的第一个 React 事件绑定

首先 构建我们的 UI

```javascript
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0,
    };
  }
  render() {
    const { counter } = this.state;
    return (
      <div>
        <div>{counter}</div>
        <button>+1</button>
      </div>
    );
  }
}

export default Counter;
```

然后我们分别通过

- 普通函数

- 匿名函数

来绑定事件 并打印出内部的 this 指向

```javascript
<button onClick={function (){console.log(this)}}>+1</button>
<button onClick={()=>{console.log(this)}}>+1</button>
```

其结果分别是

- `undefined`
- `Counter {props: {…}, context: {…}, refs: {…}, updater: {…}, state: {…}, …}`

通过打印的结果 我们就有了两种 解决 this 指向的方法

- 通过 bind 来改变 this 指向

- 直接使用匿名函数

以下是两种方法的完整代码

```javascript
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);

    this.state = {
      counter: 0,
    };

    this.increment = this.increment.bind(this);
  }
  render() {
    const { counter } = this.state;
    return (
      <div>
        <div>{counter}</div>
        <button onClick={this.increment}>+1</button>
        <button
          onClick={() => {
            this.increment();
          }}
        >
          +1
        </button>
      </div>
    );
  }

  increment() {
    this.setState({
      counter: this.state.counter + 1,
    });
  }
}

export default Counter;
```

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
