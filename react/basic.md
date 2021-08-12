> 注：本章节的 demo 都以`类组件 + TS`为例 等下一章 hook 章节后都为函数式组件

> UI 库 统一使用 `antd` https://ant.design/components/overview-cn/

> 本节的所有代码都在这个 👉<a href="https://github.com/LuckyChou710/ReStart-FE/tree/main/react/react-cra/src/components/basic">仓库</a>

## 初始化项目

使用 webpack

```zsh
npx create-react-app my-app --template typescript

# or

yarn create react-app my-app --template typescript
```

使用 vite

```zsh
npm init vite@latest my-vue-app -- --template react-ts

# or

yarn create vite my-vue-app --template react-ts
```

配置可以参考 👉<a href="https://github.com/LuckyChou710/ReStart-FE/tree/main/react">仓库 🏠</a>

如果你暂时还不需要集成 typescript 的话 可以去掉 --template typescript

## 父子组件通信

### 父组件 --> 子组件

父 --> 子 比较简单

父组件直接通过 props 来传递属性

类组件用`this.props.[属性名]`即可拿到对应的属性

函数式组件直接使用`props.[属性名]`即可

### 子组件 --> 父组件

原理和 父 --> 子 类似

在父组件用 props 向子组件传递一个函数

然后在子组件用`this.props.<函数名>`触发这个函数

以下是一个计数器累加的 🌰

父组件负责管理数据和方法

下面是组件间的通信

父组件向子组件传递 count 变量

子组件触发父组件 累加方法

```tsx
import React, { Component } from 'react';
import { Button } from 'antd';

interface IProps {
  count?: number;
  onClick?: () => void;
}

interface IState {
  count: number;
}

class ChildComponent extends Component<IProps, IState> {
  render() {
    const { count, onClick } = this.props;
    return <Button onClick={onClick}>{count}</Button>;
  }
}

class ParentComponent extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {
      count: 0,
    };
  }
  handleClick() {
    this.setState({
      count: this.state.count + 1,
    });
  }
  render() {
    return (
      <ChildComponent count={this.state.count} onClick={this.handleClick} />
    );
  }
}

export default ParentComponent;
```

如果你执行上述代码的话 你会发现数据可以正常显示 这说明父组件的数据正确的传递给了子组件

但是一旦点击了按钮 页面就会报错 你会看到如下报错信息

> TypeError: Cannot read property 'setState' of undefined

也就是 this 是 undefined

在上一讲我们就说到 在类组件中绑定事件时要注意 this 的绑定

React 并没有帮我们绑定 this 如果我们没有手动绑定 那么它就是 undefined

关于 React 为什么没有帮我们绑定 this 你可以戳 👉<a href="https://www.zhihu.com/question/300850914">这篇文章</a>

解决的方法有两种

1. 手动绑定 this 在父组件的构造函数处 手动绑定为方法 绑定 this

```tsx
constructor(props: IProps) {
  super(props);
  this.state = {
    count: 0,
  };
  this.handleClick = this.handleClick.bind(this);
}
```

2. 使用箭头函数

```tsx
<ChildComponent count={this.state.count} onClick={() => this.handleClick()} />
```

## 生命周期函数

主要参考官方的[生命周期图谱](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

具体有关生命周期的内容 我会在下几章中更新

这里 我只提最常用的几个生命周期函数以及它们的用途

- constructor

  - 初始化内部的 state

  - 为事件绑定 this

- render

  - React 的灵魂 用于描述 UI 和交互

  - props/state/forceUpdate 都会重新调用该生命周期 从而使页面更新

- shouldComponentUpdate

  - 对比更新前后数据 优化性能

- componentDidMount

  - 网络请求

- componentWillUnmount

  - 清除定时器等 优化性能

## 状态提升

状态提升是一个概念性的东西

状态指 组件间共享的一些数据

提升指 将这些状态保存在离它们最近的父组件

比如更改主题 我们就需要将主题这个状态存放在根组件下 然后通过 props 一层一层往下传递

## ref

我们实现一个简单实现一个 input 的功能

其内部数据由我们的 state 来维护

所以我们直接读 state 的值就是 input 中的值

```tsx
import React, { Component } from 'react';

interface IProps {}

interface IState {
  inputVal: string;
}

class App extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {
      inputVal: '',
    };
  }
  handleInputChange(e: any) {
    this.setState({
      inputVal: (e.target as HTMLInputElement).value,
    });
  }
  render() {
    return (
      <input
        value={this.state.inputVal}
        onChange={(e) => this.handleInputChange(e)}
      />
    );
  }
}

export default App;
```

现在我们直接通过 dom 去获取 input 的值

React 给我们提供了 ref 属性 通过这个属性 我们可以获取到元素的实例

```tsx
import React, { Component } from 'react';

interface IProps {}

interface IState {}

class App extends Component<IProps, IState> {
  private inputRef: React.RefObject<HTMLInputElement>;
  constructor(props: IProps) {
    super(props);
    this.state = {};
    this.inputRef = React.createRef();
  }
  render() {
    return (
      <>
        {/* 方式一 dom在current属性里 */}
        <input
          ref={this.inputRef}
          onChange={() => console.log(this.inputRef.current)}
        />

        {/* 方式二 dom直接在创建的ref里 */}
        <input
          ref={(inputRef) => (this.inputRef = inputRef as any)}
          onChange={() => console.log(this.inputRef)}
        />
      </>
    );
  }
}
export default App;
```

两者的区别是

- 前者 其实例在 current 属性下

- 而通过函数创建 其实例就是我们所命名的那个属性

**写在最后 在生产环境中 千万不要这样去操作 dom**

**所有可以使用声明式完成的功能都不要使用命令式**

**除非我们需要实现 聚焦 动画 等 必须要获取到 dom 的操作**

## 受控组件/非受控组件

受控组件和非受控组件一般都是针对表单元素来说的 因为它们有自己的 value 属性 可以管理自己的状态

受控的意思是指元素的状态由外部数据来维护 可理解为数据驱动视图 就是上述例子中的前者

非受控的意思是指元素的状态由自己来维护 可理解为 jq 操作 dom 来拿数据 就是上述例子中通过 ref 来操作

## context

假设 我们有一个 App 组件

然后 App 组件下有一个 HeaderWrapper 组件

HeaderWrapper 组件内部 又有一个 Header 组件

那么 如果我们想把 App 组件中的数据 到 Header 组件

数据流就要经过 HeaderWrapper 这个组件

但是这个组件是不需要 Header 组件需要的那个 props 的

尤其是当你使用了类型约束时 你会需要为传递数据的中间组件 编写它们不需要的 props 约束 😳

如果我们层层传递 那么代码如下

```tsx
import React, { Component } from 'react';

interface IProps {
  header?: string;
}

interface IState {
  header?: string;
}

class App extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {
      header: '这是Header组件需要的内容',
    };
  }
  render() {
    return <HeaderWrapper header={this.state.header} />;
  }
}

class HeaderWrapper extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    return <Header {...this.props} />;
  }
}

class Header extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    return <h1>{this.props.header}</h1>;
  }
}

export default App;
```

好吧 这只是经过了一层 我们已经感觉到了麻烦 如果是 🤔

这时 React 给我们提供了一个属性 **context** 用来解决跨组件通信的问题

常用 API 有

- React.createContext(defaultValue)

- contextType

- Provider

- Consumer

```tsx
import React, { Component } from 'react';

interface IProps {}

interface IState {}

// step1 创建一个context 可以创建多个
const HeaderContext = React.createContext({
  header: '这是Header组件需要的内容',
});

class App extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    return (
      <HeaderContext.Provider value={{ header: '这是Header组件需要的内容' }}>
        <HeaderWrapper />
        <FunHeader />
      </HeaderContext.Provider>
    );
  }
}

// 此时 我们的HeaderWrapper组件就是干净的 不再需要传递它不需要的props
// 但是 如果需要 该组件也可以在context中拿到值
class HeaderWrapper extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    return <Header {...this.props} />;
  }
}

class Header extends Component<IProps, IState> {
  // step3 在需要使用的地方 将contentType赋值成我们需要的那个context
  static contextType = HeaderContext;
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    // step 4 使用 🥰
    return <h1>{this.context.header}</h1>;
  }
}

// 如果是函数式组件 需要使用下述写法
function FunHeader() {
  return (
    <HeaderContext.Consumer>
      {(value) => <h1>{value.header}</h1>}
    </HeaderContext.Consumer>
  );
}

export default App;
```

但是 在实际开发中 我们一般不会使用 context

在生成环境下 我们一般会使用 redux / mobx

有关 context 的发展历程 你可以看 👉<a href="https://react.docschina.org/docs/legacy-context.html#updating-context">这里</a>

## 合成事件

React 中的绑定事件 onClick 等等 是 React 中的合成事件

它和原生的 onclick 事件 不同 主要是用于抹平各浏览器之间的差异

因为 React 不只是期望运行在 Web 环境 也期望运行在客户端 ios Android 等

在绑定事件时 传入的第一个参数默认就是 React 中的 event 对象

同样的 React 也封装了这个对象 为了适合所有开发场景下的使用

## dangerouslySetInnerHTML

假设有以下代码 我们想要渲染出 tag 中的 dom 元素

直接渲染的话 它会被当成字符串 渲染在页面上

我们需要使用 dangerouslySetInnerHTML 告诉 React 这是一个 dom 元素

但是也存在副作用 正如它的名字 dangerously 一样

不合时宜的使用 可能会你的页面遭受 XSS 攻击

所以忘掉这个属性吧 😛

```tsx
import React, { Component } from 'react';

interface IProps {}

interface IState {
  tag: string;
}

class App extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {
      tag: '<h2>HELLO REACT</h2>',
    };
  }
  render() {
    return (
      <>
        {/* 页面中显示 <h2>HELLO REACT</h2> */}
        {this.state.tag}

        {/* 页面正确解析h2标签 */}
        <div dangerouslySetInnerHTML={{ __html: this.state.tag }}></div>
      </>
    );
  }
}

export default App;
```

## Fragments

所有的 JSX 必须要有一个根元素包裹

如果你不想创建额外的元素 那么你就可以使用 Fragments 来包裹它们

该元素不会创建任何额外的 dom 节点 所以你对该组件的任何操作都会失效

你也可以使用简写 `<>jsx</>`

## StrictMode

使用脚手架创建项目时 默认会在跟标签外面包裹`StrictMode`

和`Fragment`一样 `StrictMode`不会创建任何 UI 元素 正如字面意思一样 它主要用于

- 识别不安全的生命周期

- 使用过时的 ref 的 API

- 检查意外的副作用

  - 开发环境下会调用两次 constructor

- 识别废弃的 findDOMNode 方法

- 检测过时的 context API

## 错误边界

错误边界依赖 `componentDidCatch` 这个生命周期函数 所以目前只有类组件能够实现错误边界

错误边界能够帮助我们在页面出错的情况下 降级 UI 而不至于页面崩溃

<details>
<summary>下面贴一段官网的 demo</summary>

```tsx
import React from 'react';

interface IProps {}

interface IErrorState {
  error: any;
  errorInfo: any;
}

interface ICountState {
  counter: number;
}

class ErrorBoundary extends React.Component<IProps, IErrorState> {
  constructor(props: IProps) {
    super(props);
    this.state = { error: null, errorInfo: null };
  }

  componentDidCatch(error: any, errorInfo: any) {
    // Catch errors in any components below and re-render with error message
    console.log('error:', error);
    console.log('errorInfo:', errorInfo);
    this.setState({
      error: error,
      errorInfo: errorInfo,
    });
    // You can also log error messages to an error reporting service here
  }

  render() {
    if (this.state.errorInfo) {
      // Error path
      return (
        <div>
          <h2>Something went wrong.</h2>
          <details style={{ whiteSpace: 'pre-wrap' }}>
            {this.state.error && this.state.error.toString()}
            <br />
            {this.state.errorInfo.componentStack}
          </details>
        </div>
      );
    }
    // Normally, just render children
    return this.props.children;
  }
}

class BuggyCounter extends React.Component<IProps, ICountState> {
  constructor(props: IProps) {
    super(props);
    this.state = { counter: 0 };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(({ counter }) => ({
      counter: counter + 1,
    }));
  }

  render() {
    if (this.state.counter === 5) {
      // Simulate a JS error
      throw new Error('I crashed!');
    }
    return <h1 onClick={this.handleClick}>{this.state.counter}</h1>;
  }
}

function App() {
  return (
    <div>
      <p>
        <b>
          This is an example of error boundaries in React 16.
          <br />
          <br />
          Click on the numbers to increase the counters.
          <br />
          The counter is programmed to throw when it reaches 5. This simulates a
          JavaScript error in a component.
        </b>
      </p>
      <hr />
      <ErrorBoundary>
        <p>
          These two counters are inside the same error boundary. If one crashes,
          the error boundary will replace both of them.
        </p>
        <BuggyCounter />
        <BuggyCounter />
      </ErrorBoundary>
      <hr />
      <p>
        These two counters are each inside of their own error boundary. So if
        one crashes, the other is not affected.
      </p>
      <ErrorBoundary>
        <BuggyCounter />
      </ErrorBoundary>
      <ErrorBoundary>
        <BuggyCounter />
      </ErrorBoundary>
    </div>
  );
}

export default App;
```

</details>
## Render Props

## Render Props

> render prop 是一个用于告知组件需要渲染什么内容的函数 prop

> https://react.docschina.org/docs/render-props.html

## 高阶组件

高阶组件就是一个函数 它接收一个组件 并返回一个新的组件

主要功能有

- 可操作所有传入的 props

- 可操作组件的生命周期

- 可操作组件的 static 方法

- 获取 refs

- 可操作 state

- 可以渲染劫持

```tsx
import React, { Component } from 'react';

interface IProps {
  theme?: string;
}

interface IState {
  theme?: string;
}

class App extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    return (
      <>
        <HeaderWrapper />
        <ArticleWrapper />
      </>
    );
  }
}

class Header extends Component<IProps, IState> {
  render() {
    return <p style={{ color: this.props.theme }}>NavBar</p>;
  }
}

class Article extends Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = {};
  }
  render() {
    return <p>Article</p>;
  }
}

// 增强props  泛型T表示接收组件的props 因为我们需要注入新的props 所以T需要继承拥有新属性的接口
function ThemeHOC<T extends IProps>(Component: React.ComponentType<T>) {
  return class extends React.Component {
    render() {
      // 为组件注入theme属性
      return <Component {...(this.props as T)} theme={'red'} />;
    }
  };
}

// 劫持生命周期方法 props state render方法
function LifeHOC<T>(Component: React.ComponentType<T>) {
  return class extends React.Component {
    constructor(props: T) {
      super(props);

      // 劫持到原组件的实例 并可以修改它
      console.log(this);

      // 可操作所有传入的props
      // 可操作组件的生命周期
      // 可操作组件的static方法
      // 获取refs
      // 可操作state
      // 可以渲染劫持
    }
    render() {
      return <Component {...(this.props as T)} />;
    }
  };
}

const HeaderWrapper = ThemeHOC(Header);
const ArticleWrapper = LifeHOC(Article);

export default App;
```

## 类型检查

如果你的项目还未使用 typescript 又想约束类型的话

你大概会使用到这个库 `prop-types`

```jsx
import React, { Component } from 'react';
import PropTypes from 'prop-types';

export default class App extends Component {
  static propTypes = {
    nickname: PropTypes.string.isRequired,
    age: PropTypes.number,
  };

  static defaultProps = {
    nickname: 'nanshu',
    age: 18,
  };

  render() {
    return (
      <div>
        <h1>{this.props.nickname}</h1>
        <h1>{this.props.age}</h1>
      </div>
    );
  }
}
```

但是`prop-types`只做 warning 层面的警告 ⚠️ 它不会打断我们的程序
