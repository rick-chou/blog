## 背景

redux 中的数据流大致是

UI —————> action（plain）—————> reducer —————> state —————> UI

action 是一个原始 js 对象（plain object）且 reducer 是一个纯函数

**但是如果存在副作用，比如 ajax 异步请求等等，那么应该怎么做？**

我们需要实现的功能也很简单 就是继续上一讲的 demo 完善 article 中的 reducer

article 组件中派发一个 get_list 的 action 然后我们需要在 store 中更新网络请求回来后的 lists

首先我们在 reducer 中处理网络请求看看行不行的通

```typescript
import * as TYPES from './actionTypes';
import { IAction } from '@type/index';
import API from '@utils/api';
const defaultState = {
  list: [],
};

export default (state = defaultState, action: IAction) => {
  switch (action.type) {
    case TYPES.GET_LIST: {
      API.getList().then((list) => {
        console.log(list);
        return { ...state, list };
      });
    }
    default:
      return state;
  }
};
```

通过点击按钮 我们确实可以看到控制台上打印出了 我们在 reducer 中发送网络请求后的打印语句

但是查看 devtools 我们会发现 store 并没有更新

也就是说 reducer 中所有改变 store 的方法走的都是同步的代码

这也不难理解 因为这会让我们的 store 变的更加可控 它不会因为异步操作的某个回掉函数在不知道什么时候改变了 store

但是 如果我们就是要发送请求 针对这种场景 我们该如果去实现呢

这个时候就可以引入中间件来帮助我们完成异步操作

redux 增加中间件处理副作用后的数据流大致如下

UI —————> action(side function) —————> middleware —————> action(plain) —————> reducer —————> state —————> UI

本文就主要介绍两种中间件 **redux-thunk** 和 **redux-saga**

## redux-thunk

### 引入

store 下的入口文件 index

```typescript
import { createStore, combineReducers, applyMiddleware } from 'redux';
import thunkMiddleware from 'redux-thunk';
import home from './home/reducers';
import article from './article/reducers';

const rootReducer = combineReducers({
  home,
  article,
});

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const enhancer = composeEnhancers(applyMiddleware(thunkMiddleware));

export default createStore(rootReducer, enhancer);
```

然后是 actionCreators

```javascript
// 这是箭头函数的语法 用来返回一个对象
() => ({})
// 如果不使用简写 就需要写成如下这种样子 这两种写法是等价的
() => {
  return {}
}
```

```typescript
import * as ACTIONS from './actionTypes';
import API from '@utils/api';
import { Dispatch } from 'redux';

export const getList = () => ({
  type: ACTIONS.GET_LIST,
});

export const setList = (list: Array<any>) => ({
  type: ACTIONS.SET_LIST,
  list,
});

export const getListSync = (list: Array<any>) => ({
  type: ACTIONS.GET_LIST_ASYNC,
  list,
});

export const getListAsync = () => {
  return async (dispatch: Dispatch) => {
    const { list } = await API.getList();
    dispatch(getListSync(list));
  };
};
```

其他所有地方都按正常使用即可 在组件内我们使用的是进行了异步操作的那个函数

通过 thunk 我们可以在 dispatch 前拦截 action

然后进行网络请求等异步操作 最后让 action 带上我们需要的数据 变成原始的 action(plain object) 再 dispatch 出去

但是这种方法的缺点也很明显 就是将异步请求的操作和 actionCreator 杂糅在了一起

对于每一个需要异步操作的 action 我们都需要在 actionCreator 中写一个对应的函数 这样会使 actionCreator 变的越来越大 不易于管理

所以对应的 又有一种中间件 **saga**

## redux-saga

在 saga 中 side effect 都移到了 saga.js 文件中 不再和 actionCreator 杂糅在一起

例如如下 saga.js 文件

```javascript
import { takeEvery, put } from 'redux-saga/effects';
import axios from 'axios';
import { getName } from './actionCreators';

// 执行副作用的函数
function* fetchGetName(action) {
  const res = yield axios.get('/__mock__.json');
  const name = res.data.data;
  yield put(getName(name));
}

// 监听GET_NAME_VALUE action 在dispatch前执行fetchGetName 最后包装成原始action
function* mySaga() {
  yield takeEvery('GET_NAME_VALUE_ASYNC', fetchGetName);
}

export default mySaga;
```

我们的 actionCreator 还是纯净的文件

```javascript
import { GET_NAME_VALUE } from './actionTypes';

export const getName = (name) => ({
  type: GET_NAME_VALUE,
  name,
});
```

再来看看 saga 怎么和 store 建立联系

```javascript
import { createStore, applyMiddleware, compose } from 'redux';
import reducer from './reducer';
import createSagaMiddleware from 'redux-saga';
import mySaga from './sagas';

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const sagaMiddleware = createSagaMiddleware();

const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware));

const store = createStore(reducer, enhancer);

sagaMiddleware.run(mySaga);

export default store;
```

注意 触发 action 和触发 saga 的 type 不能同名 不然会导致 saga 一直执行 action

然后在业务组件中 我们 dispatch 的 type 应是 saga 对应的 type
