本文参考如下文档

> <a href="https://redux.js.org/">Redux 官方文档</a>

> <a href="https://www.redux.org.cn/">Redux 中文文档</a>

## Redux 简介

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux7.png" />

Redux 由 Flux 演变而来 是一套管理公共状态的第三方工具

> 关于 Flux 思想 可参考 <a href="http://www.ruanyifeng.com/blog/2016/01/flux.html">阮一峰的网络日志</a>

虽然不是 React 官方开发 但已经成为 React 管理状态事实上的标准

### Redux 工作流程

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux9.png" />

- View 视图层内派发 action `(dispatch(action))`

- Reducer 接收到 action 进行分发和处理 返回一个新的 state 给 store

- Store 接收到新的 state 数据发生改变

- View 视图层 通过 store.subscribe 订阅 store 更新页面

具体流程可见下图

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux11.png" />

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux12.png" />

### 三大原则

#### 单一数据源

虽然 Redux 源于 Flux 架构 但是它并不是完全按照 Flux 架构去设计的

例如 Flux 架构中 允许有多个 store 但是 Redux 中只允许有一个 store 存在

所有的 state 都被存在了唯一的一个 store 中

这也就确保了数据的`可追踪`和`可预测`

#### 不可变数据

不要尝试直接修改 store 中的数据 这将会使你的应用发生不可预测的结果

唯一改变 state 的方法就是触发 action

这样 每次你的修改都会返回一个新的 store

Redux 就可以记录每一次 store 的变化 从而实现调试等功能

#### 使用纯函数

> 此函数在相同的输入值时，需产生相同的输出。函数的输出和输入值以外的其他隐藏信息或状态无关，也和由 I/O 设备产生的外部输出无关。
> 该函数不能有语义上可观察的函数副作用，诸如“触发事件”，使输出设备输出，或更改输出值以外物件的内容等。 ------ 维基百科

Reducer 只是一些纯函数 这意味着 Reducer 的结果将只受 Action 控制

再回过头来看 Redux 的官方定义

> A Predictable State Container for JS Apps ----- Redux 官方

我们会发现 这三大原则其实都只在一件事 就是 Predictable 可预测的

## Store: 它是一个单一的数据源，而且是只读的

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux6.png" />

## Action: 是“动作”的意思，它是对变化的描述

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux3.png" />

## Reducer: 它负责对变化进行分发和处理，最终将新的数据返回给 Store

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux5.png" />

## API

### creatorStore

创建 store 对象

### appleMiddleware

使用中间件 在下一讲中间件中会提到

### bindActionCreators

该 Api 用于将 action 和 dispatch 绑定 从而使组件可以无感知 Redux 的存在

```js
const { dispatch } = useDispatch();
const _bindActionCreators = bindActionCreators(
  {
    // 定义好的一些actionCreators
    addCounter,
    subCounter,
  },
  dispatch
);

// 这样就可以派发一个action了
_bindActionCreators.addCounter();
```

### combineReducers

当我们的页面变得越来越复杂的时候 可能我们需要针对模块拆分不同的 Store

这个 Api 就可以帮我们重新组合这些 Store 变成一个 Store

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux4.png" />

### connect

用于将 Store 和 Action 映射到组件的 props 上

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux1.png" />

### compose

compose 是函数式编程中的方法 用来从右到左来组合多个函数

本文只做 Redux 的入门 所以 compose 这个函数可以在函数式编程中深究

## Redux DevTools

这是一个 Chrome 的插件 可以让我们更好的调试我们的 Redux

<img src="https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/redux/redux2.png" />

## react-redux

这是一个用于将你的组件和 Redux 更方便连接的组件库

使用 如下

```tsx
import { Provider, useDispatch, useSelector } from 'react-redux';

<Provider store={store}>
  <A />
  <B />
  <C />
</Provider>;
```

如此一来 A B C 组件便都有能力获取到 Store 中的数据了

具体的用法 可以看接下来的 Demo

## Demo

这里我用一个计数器的 🌰 来快速过一遍 Redux

首先 创建我们的 store

```ts
// 导入核心API 创建Store
import { createStore } from 'redux';

interface IStore {
  count: number;
}

interface IAction {
  type: string;
  [key: string]: any;
}

// 定义我们的 Action Type
enum ACTION_TYPE {
  ADD_COUNTER = 'ADD_COUNTER',
  SUB_COUNTER = 'SUB_COUNTER',
}

// 对外暴露 Action Creators 用于组件调用
export const addCounter = (payload: number) => ({
  type: ACTION_TYPE.ADD_COUNTER,
  payload,
});

export const subCounter = (payload: number) => ({
  type: ACTION_TYPE.SUB_COUNTER,
  payload,
});

// 创建一个初始化的Store
const initStore: IStore = {
  count: 0,
};

// 创建Reducer 用于管理 View 派发过来的 Action
const reducer = (store = initStore, action: IAction) => {
  switch (action.type) {
    case ACTION_TYPE.ADD_COUNTER:
      return { ...store, count: store.count + action.payload };
    case ACTION_TYPE.SUB_COUNTER:
      return { ...store, count: store.count - action.payload };
    default:
      return store;
  }
};

// 创建 Store 这里我们还开启了 Redux DEVTools
export const store = createStore(
  reducer,
  (window as any).__REDUX_DEVTOOLS_EXTENSION_COMPOSE__()
);
```

视图层的代码如下

```tsx
import { useState } from 'react';
import { Button, Input } from 'antd';
import { Provider, useDispatch, useSelector } from 'react-redux';
import { store, addCounter, subCounter, IStore } from './store';

function Counter() {
  // 获取 Store 中的数据
  const { count } = useSelector((store: IStore) => store);
  const dispatch = useDispatch();
  const [payload, setPayload] = useState<number>(1);

  return (
    <>
      <Input
        value={payload}
        onChange={(v) => setPayload(parseInt(v.target.value))}
      />
      <Button onClick={() => dispatch(addCounter(payload))}>+</Button>
      <Button>{count}</Button>
      <Button onClick={() => dispatch(subCounter(payload))}>-</Button>
    </>
  );
}

export default function Root() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

## 思考

本文中的 reducers 都是同步代码 如果我们在发送 action 的时候 需要执行一些异步操作 这个时候应该怎么办呢

reducer 中是否可以处理异步操作呢 ？？？
