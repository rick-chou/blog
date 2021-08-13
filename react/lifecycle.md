## React 15

```
constructor()
componentWillReceiveProps() -- 过时
shouldComponentUpdate()
componentWillMount() -- 过时
componentWillUpdate() -- 过时
componentDidUpdate()
componentDidMount()
render()
componentWillUnmount()
```

再早些，还有 getDefaultProps 和 getInitState 这两个方法，

它们都是 React.createClass() 模式下初始化数据的方法。

由于这种写法在 ES6 普及后已经不常见，这里不再详细展开。

## React 16

[React16 生命周期图谱](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

在 React16 前 React 采用同步渲染的方式 每次组件更新 都会构建一颗新的 dom 树 然后进行 diff 实现对 dom 的定向更新

但是这个过程 是一个十分耗时的递归过程

有可能在这个期间 用户都无法进行任何的操作 因为这个过程抢占了主线程 浏览器无法响应其他任何操作

而 React16 用 Fiber 架构重写了核心算法

将同步渲染改为了异步渲染

查看生命周期图谱左侧 可以看到生命周期又被分为了三个阶段

- render 阶段：纯净且没有副作用，可能会被 React 暂停、终止或重新启动

- pre-commit 阶段：可以读取 DOM

- commit 阶段：可以使用 DOM，运行副作用，安排更新

用户感知到是 commit 阶段 所以这个阶段始终使用同步渲染 避免任何异步渲染带来的风险

而 render 阶段则会将一个大的更新任务拆解为许多个小任务

每当执行完一个小任务时，渲染线程都会把主线程交回去，看看有没有优先级更高的工作要处理，确保不会出现其他任务被“饿死”的情况，进而避免同步渲染带来的卡顿

因为 render 阶段的生命周期有可能被打断重新执行 所以在 render 阶段的生命周期就变得不那么”安全“了

回过头来 看看 react15 有哪些生命周期位于 render 阶段

- componentWillMount

- componentWillUpdate

- componentWillRecevieProps

因为它们阻碍了 Fiber 架构 所以在 React16 中将其弃用

并且把新增的 getDerivedStateFromProps 用 static 修饰，阻止用户在其内部使用 this
