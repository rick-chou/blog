## TODO 施工中 👷‍♀️

## 基本使用

项目目录

```
├── about/
│   ├── address/
│     ├── index.jsx
│   ├── culture/
│     ├── index.jsx
│   ├── index.jsx
├── article/
│   ├── index.jsx
├── profile/
│   ├── index.jsx
├── home/
│   ├── index.jsx
```

about 组件 我又嵌套了两个路由

```jsx
import React from 'react';
import { BrowserRouter, Link, NavLink, Route } from 'react-router-dom';
import Culture from './culture';
import Address from './address';

export default function About() {
  return (
    <BrowserRouter>
      <p>About</p>
      <Link to="/about/culture">文化</Link>
      <NavLink to="/about/address">地址</NavLink>

      <Route path="/about/culture" component={Culture}></Route>
      <Route path="/about/address" component={Address}></Route>
    </BrowserRouter>
  );
}
```

最外层的 App 组件

```jsx
import React, { Component } from 'react';
import { BrowserRouter, Link, NavLink, Route } from 'react-router-dom';
import Home from './home';
import Profile from './profile';
import About from './about';
import Article from './article';
import routes from './router';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      id: 1,
      profile: {
        name: 'chou',
        age: 18,
      },
    };
  }

  render() {
    return (
      <div>
        <BrowserRouter>
          <Link to="/">首页</Link>
          {/* 将对象作为参数传递到对应的页面 */}
          {/* 对应的页面可以通过props.location获取到这个对象 */}
          <NavLink
            to={{
              pathname: '/profile',
              state: this.state.profile,
            }}
          >
            我的
          </NavLink>
          <Link to="/about">关于</Link>
          {/* 动态路由 */}
          <Link to={`/article/${this.state.id}`}>文章</Link>
          {/* exact开启精确匹配 否则/会匹配所有路由*/}
          <Route exact path="/" component={Home}></Route>
          <Route path="/profile" component={Profile}></Route>
          <Route path="/about" component={About}></Route>
          {/* :为动态路由固定写法 */}
          <Route path="/article/:id" component={Article}></Route>
        </BrowserRouter>
      </div>
    );
  }
}
export default App;
```

## 统一管理 Router

使用过 Vue 的小伙伴应该都知道

Vue 将所有的路由都放在了一个文件中进行了统一的管理

如果在 React 中也想要将路由进行统一管理的话

就可以引入一个第三方的库 `react-router-config` [具体使用](https://www.npmjs.com/package/react-router-config)

### react-router-config

我们可以在根目录下新建一个 router 文件夹 然后在里面新建 index.js 文件 用来管理我们所有的路由

当项目复杂时 我们也可以根据组件来拆分我们的路由

```javascript
const { default: About } = require('../about');
const { default: Home } = require('../home');
const { default: Profile } = require('../profile');

const routes = [
  {
    path: '/',
    exact: true,
    component: Home,
  },
  {
    path: '/about',
    component: About,
  },
  {
    path: '/profile',
    component: Profile,
  },
];

export default routes;
```

然后在刚刚的组件中引入这个文件

```jsx
import React, { Component } from 'react';
import { BrowserRouter } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';
import routes from './router';

class App extends Component {
  render() {
    return (
      <div>
        <BrowserRouter>
          <Link to="/">首页</Link>
          <NavLink
            to={{
              pathname: '/profile',
              state: this.state.profile,
            }}
          >
            我的
          </NavLink>
          <Link to="/about">关于</Link>
          <Link to={`/article/${this.state.id}`}>文章</Link>
          {renderRoutes(routes)}
        </BrowserRouter>
      </div>
    );
  }
}

export default App;
```

如果我们的路由存在嵌套

刚刚的路由表 就需要更改为如下 给需要嵌套的路由 加上 routes 属性

顺便提一下 对于需要精确匹配的路由 需要加上`exact:true`属性

```javascript
import Address from '../about/address';
import Culture from '../about/culture';

const { default: About } = require('../about');
const { default: Home } = require('../home');
const { default: Profile } = require('../profile');

const routes = [
  {
    path: '/',
    exact: true,
    component: Home,
  },
  {
    path: '/about',
    component: About,
    routes: [
      {
        path: '/about/address',
        component: Address,
      },
      {
        path: '/about/culture',
        component: Culture,
      },
    ],
  },
  {
    path: '/profile',
    component: Profile,
  },
];

export default routes;
```

然后在对应的嵌套路由组件中 props 中会有一个 route 属性

在需要嵌套路由的组件中 我们就可以这样写

```jsx
import React from 'react';
import { BrowserRouter, Link, NavLink, Route } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';
export default function About(props) {
  console.log('culture组件:', props.route.routes);
  return (
    <BrowserRouter>
      <p>About</p>
      <Link to="/about/culture">文化</Link>
      <NavLink to="/about/address">地址</NavLink>
      {/* 因为不能将整张路由表放在这里 */}
      {renderRoutes(props.route.routes)}
    </BrowserRouter>
  );
}
```

## withRouter

如果我们在刚刚的 App 组件中 直接打印 props

```jsx
render () {

  console.log("App组件:",this.props)

  return (
    <div>
      <BrowserRouter>
        {renderRoutes(routes)}
      </BrowserRouter>
    </div>
  )
}
```

我们可以在控制台看到 它的 props 是一个空的对象

如果我们想在不是通过路由切换过来的组件中 获取到 history location match 这三个属性

这个时候就需要用到一个高阶组件 对我们的组件进行一层包裹

注意点 用 withRouter 导出的元素 需要包裹在`BrowserRouter`或`HashRouter`下

为了在一个文件下演示 将最后导出的代码 改造成如下

```jsx
const MyApp = withRouter(App);

class _AppWrapper extends Component {
  render() {
    return (
      <BrowserRouter>
        <MyApp />
      </BrowserRouter>
    );
  }
}

export default _AppWrapper;
```

## 一些零碎点

- 如果想给选中的路由加样式 可以使用 NavLink 默认会给选中的样式加上 active class 如果想自定义这个 class 名字的话 可以使用 activeClassName
- Link 和 NavLink 最终会被渲染成一个 a 元素 暂不支持 Vue 中 类似传入 type 可以渲染成其他元素
- Redirect 可以实现路由的重定向 可以用作登录界面的判断
- 用 Switch 包裹的路由 会在匹配到第一个合适的路由后就停止匹配
- React Router 中的 history 属性 和 window 对象下的 history 不一样 这是 React Router 进行包装后的合成对象
