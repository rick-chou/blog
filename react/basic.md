> 注：本章节的 demo 都以`类组件 + TS`为例 等下一章 hook 章节后都为函数式组件
> UI 库 统一使用 `antd`

## 父子组件通信

### 父组件 -> 子组件

父 --> 子 比较简单

父组件直接通过 props 来传递属性

类组件用`this.props.[属性名]`即可拿到对应的属性

函数式组件直接使用`props.[属性名]`即可

### 子组件 -> 父组件

原理和 父 -> 子 类似

在父组件用 props 向子组件传递一个函数

然后在子组件用`this.props.<函数名>`触发这个函数

以下是一个计数器累加的 🌰

父组件负责管理数据和方法

父组件 --> 子组件 count 变量

子组件 触发 父组件的累加方法

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

props state 的改变 和 forceUpdate() 将会重新调用`render()`生命周期函数 具体内容可见下几讲

- constructor

  - 初始化内部的 state

  - 为事件绑定 this

- shouldComponentUpdate

  - 对比更新前后数据 优化性能

- componentDidMount

  - 网络请求

- componentWillUnmount

  - 清除定时器等 优化性能

## 状态提升

状态提升只是一个概念性的东西

状态就是指 子组件间共享的一些数据

提升就是指 将这些状态保存在离它们最近的父组件

比如更改主题 我们就需要将主题这个状态存放在根组件下 然后通过 props 一层一层往下传递

## ref

现在 我们简单的实现一个 DEMO

有一个 输入框 和 获取用户名的按钮

通过点击按钮 获取用户名

```jsx
export default class Example extends Component {
  constructor(props) {
    super(props);

    this.state = {
      username: '',
    };
  }

  render() {
    return (
      <>
        <input
          name="username"
          value={this.state.username}
          onChange={(e) => this.handleUsernameChange(e)}
        />
        <button onClick={() => this.showName()}>GET NAME</button>
      </>
    );
  }

  handleUsernameChange(e) {
    this.setState({
      username: e.target.value,
    });
  }

  showName() {
    alert(`U NAME IS ${this.state.username}`);
  }
}
```

到此为止 我们所有的数据 都是来自于组件内部维护的 state

那么我们能否直接通过 标签自身 来实现这个功能呢

React 向我们提供了 **ref** 可以来获取 dom

ref 主要有三种实现方式可以来获取 dom

```jsx
constructor(props) {
    super(props)

    this.usernameRef = React.createRef()
  }

  render() {
    return (
      <>
        {/* ref 后可以等于 字符串/对象/函数 */}
        {/* 方式一 字符串 */}
        {/* 此方法不推荐 在后续版本中可能会被移除 */}
        <input ref="usernameRef" />

        {/* 方式二 对象 */}
        <input ref={this.usernameRef} />

        {/* 方式三 函数 */}
        <input ref={(_usernameRef) => (this._usernameRef = _usernameRef)} />
        <button onClick={() => this.showName()}>GET NAME</button>
      </>
    )
  }
```

方式二 和 方式三的区别是

- 通过`React.createRef()`创建 其实例在 current 属性下
- 而通过函数创建 其实例就是我们所命名的那个属性

我们可以通过打印 this 来看一下 三者之间的区别（当然 第一种已经不推荐）

```
  context: {}
  props: {}
  refs: {usernameRef: input}  // 方式一
  state: null
  updater: {isMounted: ƒ, enqueueSetState: ƒ, enqueueReplaceState: ƒ, enqueueForceUpdate: ƒ}
  usernameRef: {current: input} // 方式二
  _reactInternalInstance: {_processChildContext: ƒ}
  _reactInternals: FiberNode {tag: 1, key: null, stateNode: Example, elementType: ƒ, type: ƒ, …}
  _usernameRef: input // 方式三
```

**写在最后 在生产中 千万不要这样去操作 dom**

**所有可以使用声明式完成的功能都不要使用命令式**

**除非我们需要实现 聚焦 动画 等 必须要获取到 dom 的操作**

## context

假设 我们有一个 App 组件

然后 App 组件下有一个 HeaderWrapper 组件

HeaderWrapper 组件 内部 又有一个 Nav 组件

那么 如果我们共享 App 组件 中的数据 到 Nav 组件

就要 经过 HeaderWrapper 组件 传递到 Nav 组件

传统的代码如下

我们可以用 `{...this.props}` 的写法 将 props 传递给子组件

```jsx
class HeaderWrapper extends Component {
  render() {
    return (
      <>
        <Nav {...this.props} />
      </>
    );
  }
}

class Nav extends Component {
  render() {
    return <>name: {this.props.name}</>;
  }
}

export default class App extends Component {
  render() {
    return (
      <div>
        <HeaderWrapper name={'chou'} />
      </div>
    );
  }
}
```

但是 HeaderWrapper 组件 可能不需要这一层的数据 但是 我们在传递数据中却要将数据传递给它

这时 React 给我们提供了一个属性 **context** 用来解决

常用 API

- React.createContext(defaultValue)

- contextType

- Provider

- Consumer

```jsx
// 第一步 我们创建一个context对象 可以传入一个默认值
const UserContext = React.createContext({
  name: 'defaultName',
});

class HeaderWrapper extends Component {
  render() {
    return (
      <>
        <Nav {...this.props} />
        <List />
      </>
    );
  }
}

class Nav extends Component {
  // 第二步 在需要 使用的地方 使用contextType
  static contextType = UserContext;
  render() {
    return (
      <>
        <div>Nav</div>
      </>
    );
  }
  componentDidMount() {
    console.log(this);
  }
}

// 如果是函数式组件 需要使用这种特殊写法
// 我们可以打印出value 来观察它确实拿到了context中的数据
function List() {
  return (
    <UserContext.Consumer>
      {(value) => {
        console.log(value);
      }}
    </UserContext.Consumer>
  );
}

export default class Example extends Component {
  constructor(props) {
    super(props);

    this.state = {
      name: 'chou',
    };
  }

  render() {
    return (
      // 第三步 用Provider包裹需要共享数据的组件
      <UserContext.Provider value={this.state}>
        <HeaderWrapper />
      </UserContext.Provider>
    );
  }
}
```

但是 在实际开发中 我们不会使用 context

在下一章我就会介绍到 在开发中 用来管理状态的 **Redux**

而它的模式 就和 context 类似

## 合成事件

React 中 绑定事件的 onClick 等等 其实是 React 中的合成事件

它和原生的 onclick 事件 不同 主要是用于抹平各浏览器之间的差异

同时 React 不只是期望运行在 Web 环境 也期望运行在客户端 ios Android 等

所以 React 重新封装了这些事件

用一套代码 来适用所有场景

## event

在绑定事件时 传入的第一个参数默认就是 React 中的 event 对象

同样的 React 也封装了这个对象 为了适合所有开发场景下的使用

```jsx
export default class Example extends Component {
  render() {
    return (
      <div>
        <button onClick={(e) => this.getEvent(e)}>Get Event</button>
      </div>
    );
  }

  getEvent(e) {
    console.log(e);
    // SyntheticBaseEvent {_reactName: "onClick", _targetInst: null, type: "click", nativeEvent: MouseEvent, target: button, …}
  }
}
```

## 高阶组件

高阶组件就是一个函数 它接收一个组件 并返回一个新的组件

主要功能有

- 可操作所有传入的 props

- 可操作组件的生命周期

- 可操作组件的 static 方法

- 获取 refs

- 可操作 state

- 可以渲染劫持

```jsx
import React from 'react';

const NavBar = ThemeHOC(
  class extends React.Component {
    render() {
      return (
        <>
          <p style={{ color: this.props.theme }}>NavBar</p>
        </>
      );
    }
  }
);

const Message = LifeHOC(
  class extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        username: 'chou',
      };
    }
    render() {
      return (
        <>
          <p>Message</p>
        </>
      );
    }

    componentDidMount() {
      console.log('我是原来的componentDidMount，我执行了');
    }
  }
);

// 增强props
function ThemeHOC(WrappedComponent) {
  return class extends React.Component {
    render() {
      return <WrappedComponent theme={'red'} />;
    }
  };
}

// 劫持生命周期方法 props state render方法
function LifeHOC(WrappedComponent) {
  return class extends WrappedComponent {
    constructor(props) {
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
  };
}

export default class App extends React.Component {
  render() {
    return (
      <>
        <NavBar />
        <Message name={'chou'} />
      </>
    );
  }
}
```

## 受控组件/非受控组件

受控组件的意思 主要是针对表单这些元素

其 value 由 state 来维护 但是也会造成每一个表单都需要编写事件去更新数据的麻烦

非受控组件 则是 表单的数据由表单自己去维护

主要是 通过 ref 来实现

官方主要推荐受控组件的方式

## dangerouslySetInnerHTML

假设有以下代码 我们想要渲染出 tag 中原有的样式

直接渲染的话 它会被当成字符串 渲染在页面上

需要使用 dangerouslySetInnerHTML

但是也存在副作用 正如它的名字 dangerously 一样

不合时宜的使用 可能会你的页面遭受 XSS 攻击

```jsx
export default class Example extends Component {
  constructor(props) {
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
```

## 动手实现 Vue 中的 slot

第一版 所有嵌套在组件内部的子组件 都会在`props.children`这个属性内

我们可以通过 `props.children[index]` 来拿对应位置的组件

缺点：使用者 必须 **按顺序** 传入组件

```jsx
class TabBar extends Component {
  render() {
    return (
      <div>
        <div className="tab-bar">
          <Fragment className="tab-bar-img">{this.props.children[0]}</Fragment>
          <Fragment className="tab-bar-title">
            {this.props.children[1]}
          </Fragment>
          <Fragment className="tab-bar-more">{this.props.children[2]}</Fragment>
        </div>
      </div>
    );
  }
}

export default class Example extends Component {
  render() {
    return (
      <>
        <TabBar>
          <span>头像</span>
          <span>标题</span>
          <span>更多</span>
        </TabBar>
      </>
    );
  }
}
```

Vue 中有具名组件的概念 我们继续改进代码来实现吧

```jsx
class SlotSwapper extends Component {
  render() {
    const { leftSlot, midSlot, rightSlot } = this.props;
    return (
      <>
        <span className="leftSlot">{leftSlot}</span>
        <span className="midSlot">{midSlot}</span>
        <span className="rightSlot">{rightSlot}</span>
      </>
    );
  }
}

export default class Example extends Component {
  render() {
    return (
      <SlotSwapper
        leftSlot={<span>这是左边的插槽</span>}
        midSlot={<span>这是中间的插槽</span>}
        rightSlot={<span>这是右边的插槽</span>}
      />
    );
  }
}
```

我们可以将 JSX 作为 props 传入给子组件

然后在子组件解构 这样就可以拿到每一个对应的 slot 而不用去考虑顺序的问题

## 动手实现 Vue 中的 v-show

Vue 中 有两种显示隐藏元素的指令 分别是

- v-show (元素并没有被移除)
- v-if / v-else (元素被移除了)

而我们在 React 中 用条件渲染 时 默认就是使用了 v-if 这种模式

如果这个操作是频繁的 大量的 创建元素 和 销毁元素 无疑是浪费性能的

下面是简单的 条件渲染的 DEMO

```jsx
export default class Example extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isShow: true,
      text: 'HIDE',
    };
  }

  render() {
    return (
      <>
        {this.state.isShow && <div>NOW U SEE ME</div>}
        <button onClick={() => this.handleHideClick()}>
          {this.state.text}
        </button>
      </>
    );
  }

  handleHideClick() {
    this.setState({
      isShow: !this.state.isShow,
      text: this.state.isShow ? 'SHOW' : 'HIDE',
    });
  }
}
```

现在我们动手来改造上面这段代码 让它具有 v-show 这种功能

思路：我们可以动态决定组件的 style

```jsx
<div style={{ display: this.state.isShow ? 'block' : 'none' }}>
  NOW U SEE ME
</div>
```

## StrictMode

使用 create-react-app 脚手架创建项目时 默认会在跟标签外面包裹`StrictMode`

和`Fragment`一样 `StrictMode`不会创建任何 UI 元素 正如字面意思一样 它主要用于

- 识别不安全的生命周期

- 使用过时的 ref 的 API

- 检查意外的副作用

  - 开发环境下会调用两次 constructor

- 识别废弃的 findDOMNode 方法

- 检测过时的 context API
