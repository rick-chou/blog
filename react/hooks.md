## 背景

- 类组件需要继承 class，函数组件不需要
- 类组件可以访问生命周期方法，函数组件不能
- 类组件中可以获取到实例化后的 this，并基于这个 this 做各种各样的事情，而函数组件不可以
- 类组件中可以定义并维护 state（状态），而函数组件不可以 （hook 之后改变了这种情况）
- ......

类组件的状态 一般由组件内部维护 这样就会造成组件的复用性很差

而函数式组件 又无法保存状态 比如如下代码

```javascript
function add(n) {
  const result = 0;
  return result + 1;
}

add(1); //1
add(1); //1
```

我们无法在函数中保存 result 的状态 因为每一次执行函数时 都会重新初始化 result

这个时候 hook 就横空出世了 它想要解决的问题就是

让函数式组件拥有类似类组件的功能

说得更具体一点，函数组件更加契合 React 框架的设计理念

我们来看一个简单的 demo

```jsx
import React from 'react';

class ProfilePageClass extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

  handleClick = () => {
    setTimeout(this.showMessage, 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}

function ProfilePageFunction(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return <button onClick={handleClick}>Follow</button>;
}

export default class App extends React.Component {
  state = {
    user: 'lucky',
  };
  render() {
    return (
      <>
        <label>
          <b>Choose profile to view: </b>
          <select
            value={this.state.user}
            onChange={(e) => this.setState({ user: e.target.value })}
          >
            <option value="lucky">lucky</option>
            <option value="chou">chou</option>
            <option value="ramires">ramires</option>
          </select>
        </label>
        <h1>Welcome to {this.state.user}’s profile!</h1>
        <p>
          <ProfilePageFunction user={this.state.user} />
          <b> (function)</b>
        </p>
        <p>
          <ProfilePageClass user={this.state.user} />
          <b> (class)</b>
        </p>
        <p>Can you spot the difference in the behavior?</p>
      </>
    );
  }
}
```

我们通过 setTimeout 将预期中的渲染推迟了 3s，打破了 this.props 和渲染动作之间的这种时机上的关联

进而导致类组件在渲染时捕获到的是一个错误的、修改后的 this.props

而函数式组件可以确保在任何时机下读取到的 props，都是最初捕获到的那个 props

所以函数组件是一个更加匹配其设计理念、也更有利于逻辑拆分与重用的组件表达形式

## useState

```tsx
import React, { useState } from 'react';
import { Statistic, Button } from 'antd';
import { LikeOutlined } from '@ant-design/icons';

const Counter: React.FunctionComponent = () => {
  const [count, setCount] = useState<number>(0);

  const addCountSync = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  };

  const addCountAsync = () => {
    setCount((preState) => preState + 1);
    setCount((preState) => preState + 1);
    setCount((preState) => preState + 1);
    setCount((preState) => preState + 1);
  };

  return (
    <>
      <Statistic value={count} prefix={<LikeOutlined />} />
      <Button onClick={() => setCount(count + 1)}>ADD COUNTER</Button>
      <Button onClick={() => addCountSync()}>ADD COUNTER SYNC</Button>
      <Button onClick={() => addCountAsync()}>ADD COUNTER ASYNC</Button>
    </>
  );
};

export default Counter;
```

## useReducer

```tsx
/**
 * useReducer
 * 和redux没有关系 组件之间不共享数据
 * 是useState的一种替代方案
 * 对于复杂的业务 使用useReducer比useState会有更好的可读性
 */

import React, { useReducer } from 'react';
import { Button } from 'antd';

enum Actions {
  ADD_COUNT_VALUE = 'ADD_COUNT_VALUE',
  SUB_COUNT_VALUE = 'SUB_COUNT_VALUE',
}

interface IState {
  count: number;
}

interface IAction {
  type: Actions;
  payload?: number;
}

const myReducers = (state: IState, action: IAction) => {
  const { payload = 1 } = action;
  switch (action.type) {
    case Actions.ADD_COUNT_VALUE:
      return {
        ...state,
        count: state.count + payload,
      };
    case Actions.SUB_COUNT_VALUE:
      return {
        ...state,
        count: state.count - payload,
      };
    default:
      return state;
  }
};

const Count = () => {
  const [state, dispatch] = useReducer(myReducers, { count: 0 });

  return (
    <div>
      <p>当前计数：{state.count}</p>
      <Button
        onClick={() => dispatch({ type: Actions.ADD_COUNT_VALUE, payload: 2 })}
      >
        +2
      </Button>
      <Button onClick={() => dispatch({ type: Actions.SUB_COUNT_VALUE })}>
        -1
      </Button>
    </div>
  );
};

export default function App() {
  return (
    <>
      <Count />
      <Count />
    </>
  );
}
```

## useEffect

```tsx
/**
 * useEffect
 * 允许函数组件执行副作用操作 在一定程度上弥补了生命周期的缺席
 * 可以接收两个参数，分别是回调函数与依赖数组 useEffect(callBack, [])
 * 第一个参数 函数形式 可实现等同于componentDidMount shouldComponentUpdate componentWillUnmount
 * 并且可以返回一个函数 用来消除副作用 类似componentWillUnmount 可以做一些事件的解绑 定时器的关闭等
 * 第二个参数 数组 状态依赖项 实现性能优化 如果传[] 则等同于不开启shouldComponentUpdate
 */

import React, { FC, useEffect, useState } from 'react';

import { Button, Tag } from 'antd';

const App: FC = () => {
  const [state, setState] = useState<Number[]>([]);
  const [tag, setTag] = useState<string>('Tag');
  useEffect(() => {
    console.log('state更新了');

    return () => {
      console.log('componentWillUnmount');
    };
  }, [state]);
  return (
    <div>
      <Button
        onClick={() =>
          setState((prevState) => [
            ...prevState,
            Math.trunc(Math.random() * 10),
          ])
        }
      >
        change state
      </Button>
      <Button onClick={() => setTag('new Tag')}>change tag</Button>

      <Tag color="orange">{tag}</Tag>
      {state.map((item, idx) => {
        return (
          <Tag color="orange" key={idx}>
            {item}
          </Tag>
        );
      })}
    </div>
  );
};

export default App;
```

## useContext

```tsx
/**
 * useContext
 * 可以在子组件之间共享数据
 * 1)在组件外创建context对象 const AppContext = React.createContext({})
 * 2)父组件内使用context对象下的Provider 并赋值
 * 3)在子组件内过去context对象 React.useContext(AppContext)
 */

import React from 'react';

interface ITheme {
  theme: string;
}

const AppContext = React.createContext<ITheme>({
  theme: 'red',
});

const NavBar = () => {
  const { theme } = React.useContext(AppContext);
  return (
    <div>
      <h2 style={{ color: theme }}>NavBar</h2>
    </div>
  );
};

const Message = () => {
  const { theme } = React.useContext(AppContext);
  return (
    <div>
      <h2 style={{ color: theme }}>Message</h2>
    </div>
  );
};

export default function App() {
  return (
    <AppContext.Provider value={{ theme: 'red' }}>
      <NavBar />
      <Message />
    </AppContext.Provider>
  );
}
```

## useRef

```tsx
/**
 * useRef
 * 获取结点
 * 保存数据
 */

import { Button, Input } from 'antd';
import React, { FC, useRef, useState } from 'react';

const App: FC = () => {
  const [count, setCount] = useState<number>(0);
  const numRef = useRef<number>(count);
  const domRef = useRef<HTMLInputElement | null>(null);
  const antdRef = useRef<Input | null>(null);
  const showRef = () => {
    console.log(domRef.current, antdRef.current);
  };

  return (
    <div>
      <input defaultValue="h5" ref={domRef} />
      <Input defaultValue="antd" ref={antdRef} style={{ width: 200 }}></Input>
      <Button onClick={() => showRef()}>showRef</Button>

      <p>count : {count}</p>

      {/* numRef的值始终引用最初的那个count */}
      <p>numRef : {numRef.current}</p>
      <Button onClick={() => setCount(count + 1)}>+1</Button>
    </div>
  );
};

export default App;
```

## useCallback

在之前的章节中 我们用 shouldComponentUpdate 实现过性能优化

也用过 PureComponent 和 memo 来实现

在使用 memo 的时候 React 内部使用的是浅层比较

对于函数和对象 其实不管怎样都是不会相等的

```javascript
() => {} === () => {} // false
{} === {} // false
[] === [] // false
1 === 1 // true
'1' === '1' // true
```

所以假设 我们在父组件中将**引用数据类型**当作 props 传递给子组件时

这时候 子组件的 memo 就会失效

而在 hook 中 对于函数 我们可以用 useCallback 将函数包装

对于对象 我们可以使用 useMemo 将对象包装

注：滥用 useCallback 不仅不会优化性能 还会造成性能浪费

```tsx
/**
 * useCallback
 * 有一个父组件，其中包含子组件，子组件接收一个函数作为props；
 * 通常而言，如果父组件更新了，子组件也会执行更新；但是大多数场景下，更新是没有必要的，
 * 我们可以借助useCallback来返回函数，然后把这个函数作为props传递给子组件；这样，子组件就能避免不必要的更新
 * useCallback(()=>{},[])
 * 第一个参数 是一个函数
 * 第二个参数 是一个数组 是这个函数的依赖项 只有依赖项更新 函数才会重新执行
 */

import { Button } from 'antd';
import React, { useState, useCallback, memo } from 'react';

interface IProps {
  getSum: () => number;
}

const Child1 = memo(({ getSum }: IProps) => {
  console.log('我是子组件 在父组件中没使用useCallback');
  return (
    <div>
      <Button onClick={getSum}>没使用useCallback</Button>
    </div>
  );
});

const Child2 = memo(({ getSum }: IProps) => {
  console.log('我是子组件 在父组件中使用了useCallback');
  return (
    <div>
      <Button onClick={getSum}>使用了useCallback</Button>
    </div>
  );
});

const Parent = () => {
  const [num, setNum] = useState(0);
  const [max, setMax] = useState(100);

  const getSum1 = () => {
    console.log('getSum1方法执行了');
    let sum = 0;
    for (let i = 0; i < max; i++) {
      sum += i;
    }
    return sum;
  };

  const getSum2 = useCallback(() => {
    console.log('getSum2方法执行了');
    let sum = 0;
    for (let i = 0; i < max; i++) {
      sum += i;
    }
    return sum;
  }, [max]);

  return (
    <div>
      <p>sum : {getSum1()}</p>
      <p>sum : {getSum2()}</p>
      <p>num : {num}</p>
      <Button onClick={() => setNum(num + 1)}>change num</Button>
      <Button onClick={() => setMax((pre) => pre * 2)}>change max</Button>
      <Child1 getSum={getSum1} />
      <Child2 getSum={getSum2} />
    </div>
  );
};

export default Parent;
```

## useMemo

```tsx
/**
 * useMemo
 * 有一个父组件，其中包含子组件，子组件接收一个对象作为props；
 * 通常而言，如果父组件更新了，子组件也会执行更新；但是大多数场景下，更新是没有必要的，
 * 我们可以借助useMemo来返回函数，然后把这个对象作为props传递给子组件；这样，子组件就能避免不必要的更新
 * useMemo(()=>({}),[])
 * 第一个参数 是一个函数 返回一个对象
 * 第二个参数 是一个数组 包含了这个对象的依赖项 只有依赖项更新 函数才会重新执行
 */

import { Button } from 'antd';
import React, { useState, useMemo, memo } from 'react';

interface IProps {
  age?: number;
  stu?: { name: string; age: number };
}

// 接收的是基本数据类型 memo生效
const Child = memo(({ age }: IProps) => {
  console.log('props为基本数据类型的子组件渲染了');
  return (
    <div>
      <h1>props为基本数据类型</h1>
      <h2>age:{age}</h2>
    </div>
  );
});

// 父组件中没有使用useMemo 子组件的memo失效
const Child1 = memo(({ stu }: IProps) => {
  console.log('props为引用数据类型 未使用useMemo');
  return (
    <div>
      <h1>props为引用数据类型 未使用useMemo</h1>
      <h2>
        {stu?.name}:{stu?.age}
      </h2>
    </div>
  );
});

// 父组件中使用了useMemo 子组件的memo生效
const Child2 = memo(({ stu }: IProps) => {
  console.log('props为引用数据类型 使用useMemo');
  return (
    <div>
      <h1>props为引用数据类型 使用useMemo</h1>
      <h2>
        {stu?.name}:{stu?.age}
      </h2>
    </div>
  );
});

const Parent = () => {
  const [count, setCount] = useState(0);
  const [age, setAge] = useState(18);
  const [name, setName] = useState('lucky');

  const stu1 = {
    name,
    age,
  };

  const stu2 = useMemo(
    () => ({
      name,
      age,
    }),
    [age]
  );

  return (
    <div>
      <Child age={age} />
      <Child1 stu={stu1} />
      <Child2 stu={stu2} />
      <h1>{count}</h1>
      <Button onClick={() => setCount(count + 1)}>change count</Button>
      <Button onClick={() => setAge(age + 1)}>change age</Button>
      <Button onClick={() => setName('lucky chou')}>change name</Button>
    </div>
  );
};

export default Parent;
```

## 自定义 hook

自定义 hook 的函数名 必须以 use 开头 比如下面是一个打印组件创建/销毁的 useLogger hook

```jsx
import React, { useEffect, useState } from 'react';

const useLogger = (componentName) => {
  useEffect(() => {
    console.log(`${componentName}组件被创建了`);

    return () => {
      console.log(`${componentName}组件被销毁了`);
    };
  });
};

const Header = () => {
  useLogger('Header');

  return (
    <div>
      <h2>Header</h2>
    </div>
  );
};

const Footer = () => {
  useLogger('Footer');

  return (
    <div>
      <h2>Footer</h2>
    </div>
  );
};

export default function App() {
  const [show, setShow] = useState(true);
  return (
    <div>
      <button onClick={() => setShow(!show)}>{show ? 'hidden' : 'show'}</button>
      {show && <Header />}
      {show && <Footer />}
    </div>
  );
}
```

## Hooks 使用注意点

## 总结

- 告别难以理解的 Class

  - this

  - 生命周期

- 解决业务逻辑难以拆分的问题

  - 逻辑一度与生命周期耦合在一起

```jsx
componentDidMount() {
// 1. 这里发起异步调用
// 2. 这里从 props 里获取某个数据，根据这个数据更新 DOM
// 3. 这里设置一个订阅
// 4. 这里随便干点别的什么
// ...
}
componentWillUnMount() {
  // 在这里卸载订阅
}
componentDidUpdate() {
  // 1. 在这里根据 DidMount 获取到的异步数据更新 DOM
  // 2. 这里从 props 里获取某个数据，根据这个数据更新 DOM（和 DidMount 的第2步一样）
}
```

- 使状态逻辑复用变得简单可行

  - HOC（高阶组件）

  - Render Props

- 函数组件从设计思想上来看，更加契合 React 的理念。
