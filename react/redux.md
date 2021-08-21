Store: 它是一个单一的数据源，而且是只读的

Action: 是“动作”的意思，它是对变化的描述

Reducer: 它负责对变化进行分发和处理，最终将新的数据返回给 Store

appleMiddleware

bindActionCreators

combineReducers

compose

creatorStore

## Redux 简介

![](https://gitee.com/LuckyChou/blog-images/raw/master/react/redux.png)

Redux 是一套管理公共状态的第三方工具 虽然不是 React 官方开发 但已经成为 React 管理状态事实上的标准

类似于 Vue 的 Vuex

### Redux 工作流程

- 事件内定义 action 并 dispatch 出去

- reducer 接收到 action 进行操作 返回一个新的 state 给 store

- store 接收到新的 state 数据发生改变

- 页面通过 store.subscribe 订阅 store 更新页面

我们将 Redux 的整个工作流程类比成一个借书的流程

可以看成是如下的操作

### Store

图书馆管理员

他有一本记录借书的本子 reducer

上面写了借书有关的信息 action

![](https://gitee.com/LuckyChou/blog-images/raw/master/react/redux-store.png)

### React Components

借书的人

### Action Creators

借书人发起借书的申请

```javascript
{
  type: BORROW_BOOKS,
  text: 'I want to borrow a book'
}
```

**改变 store 中数据唯一的方法就是 component 提交 action**

reducer 中根据 action 中的 type 值来区分请求 并执行具体的操作

### Reducer

图书管管理员的笔记 记录着各种借书还书的记录

```javascript
export default (state = defaultState, action) => {
  switch (action.type) {
    case BORROW_BOOKS:
      // 不能修修改state 只能返回一个新的state
        const newState = JSON.parse(JSON.stringify(state))
        // do Something......
        return newState
      })
    default:
      return state
  }
}
```

原则：reducer 中不能修改 state 只能返回一个新的 state

为了防止在操作中不小心修改 state 对象 我们可以引入`immutable.js`库

## Demo && Comment

现在我们用 Redux 写一个 Comment 的 Demo

### Redux DevTools

在开始改造我们的项目前 我们再安装一个 chrome 插件

它可以使我们的 Redux 调试 变的极为方便

在创建 store 的方法中传入第二个参数

`window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()`

```javascript
const store = createStore(
  reducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

再次启动项目 即可在浏览器调试工具中看到有 redux 选项

好啦 准备工作都完成啦 接下来 我们开始吧

我们要实现的例子很简单就是借助 redux 来管理我们的用户名和列表

### 创建 store

我们在项目中创建 store 文件夹

在目录下新建

```
├─ Home
  ├─ actionCreators.ts
  ├─ reducers.ts
  ├─ actionTypes.ts
├─ Article
  ├─ actionCreators.ts
  ├─ reducers.ts
  ├─ actionTypes.ts
```

#### index.ts

我们在这个文件中创建 store 并引入 redux 库中给我们提供的`combineReducers`工具函数来合并这两个 reducers

```javascript
import { createStore, combineReducers } from 'redux';
import home from './home/reducers';
import article from './article/reducers';

const rootReducer = combineReducers({
  home,
  article,
});

export default createStore(
  rootReducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

`createStore`中第一个参数传入 reducer

第二个参数可以传入 enhancer 这里我们传入 Redux DevTools 有关的参数

#### reducer.js

reducer 用于处理每一个来自组件派发的 action 它将决定每一次 dispatch 后 store 如何变化

这里以 home 页面改变 name 的 action 为例子

```typescript
import { SET_USER_NAME } from './actionTypes';
import { IAction } from '@type/index';

const defaultState = {
  username: 'chou',
};

export default (state = defaultState, action: IAction) => {
  switch (action.type) {
    case SET_USER_NAME:
      return { ...state, username: action.username };
    default:
      return state;
  }
};
```

其中 defaultState 为数据的默认值

reducer 文件中只做一件事 就是处理 component 中提交的 action 请求

它导出一个方法

第一个参数 state 为 store 中的旧数据

第二个参数 action 为 component 提交的改变 store 的 action 请求

再次重申：reducer 可以接受 store 中的数据

但是绝不能修改 store 中的数据

我们可以在操作前拷贝一份 store 中数据

然后再对这份数据进行修改 最后 return 出去

#### actionTypes.js

管理着我们所有 action 的 type

```javascript
export const SUBMIT_COMMENT_VALUE = 'submit_comment_value';
export const DELETE_COMMENT_ITEM = 'delete_comment_item';
```

#### actionCreators.js

统一管理着我们创建 action 的方法

```typescript
export const SET_USER_NAME = 'SET_USER_NAME';
```

如果为使用 react-redux 连接库

那么你大概会需要用到以下三个 api

- store.dispatch
- store.getState
- store.subscribe

它们分别用来派发 action 获取 store 和监听 store 的变化

我们一般会将 store.getState 放在构造函数中用于初始化时获取 store

然后使用 store.subscribe 这个方法监听 store 的变化 并更新 store 保持组件中的 store 时刻与最新的一致

demo 如下

```typescript
constructor(props) {
    super(props)
    this.state = store.getState()
    this.handleStoreChange = this.handleStoreChange.bind(this)
    store.subscribe(this.handleStoreChange)
  }
```

但是为了方便使用 我们一般会再引入一个第三方库 `react-redux`

使用这个库可以让我们的组件与 store 之间的连接更为顺畅

使用起来也很简单

首先 我们在根组件处 调用 Provider

```tsx
import React from 'react';
import RouterTemp from '@router/temp';
import { RouteConfigComponentProps } from 'react-router-config';
import { Provider } from 'react-redux';
import store from '@store/redux/index';
const index = (props: RouteConfigComponentProps) => {
  return (
    <Provider store={store}>
      <RouterTemp routerName="/store" renderRoutes={props!.route!.routes} />
    </Provider>
  );
};
export default index;
```

然后在需要用到 store 的组件中 使用 react-redux 提供的高阶组件 connect 导出我们的组件即可

mapStateToProps 和 mapDispatchToProps 两个函数需要我们去实现 它们用于将 store 和 dispatch 映射到我们组件的 props 中

demo 如下

```tsx
import React from 'react';
import { connect } from 'react-redux';
import * as ACTIONS from '@store/redux/home/actionCreators';
import { Button } from 'antd';

interface IProps {
  username: string;
  setUsername: (username: string) => void;
}

const Home = (props: IProps) => {
  return (
    <div>
      <h1>Home</h1>
      <h2>{props.username}</h2>
      <Button onClick={() => props.setUsername('lucky')}>change name</Button>
    </div>
  );
};

const mapStateToProps = (state: any) => ({
  username: state.home.username,
});

const mapDispatchToProps = (dispatch: any) => ({
  setUsername: (payload: string) => dispatch(ACTIONS.setUsername(payload)),
});

export default connect(mapStateToProps, mapDispatchToProps)(Home);
```

## 补充

- immutable.js
- combineReducers

immutable.js 可以防止我们对 state 进行修改 破坏了 State 是只读的这个原则

具体的 API 可参考[官网](https://immutable-js.github.io/immutable-js/)

## 思考

本文中的 demo 都是同步代码 如果我们在发送 action 的时候 需要执行一些异步操作 这个时候应该怎么办呢

reducer 中是否可以处理异步操作呢？？？
