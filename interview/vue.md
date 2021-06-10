## MVVM（数据驱动视图的方式）

<img src="http://images4.10qianwan.com/10qianwan/20190608/b_0_201906080345393289.jpg"  />

- MVVM 是 Model-View-ViewModel 的缩写

- Model：代表数据模型，也可以在 Model 中定义数据修改和操作的业务逻辑。

  我们把 Model 称为数据层，因为它只关心数据，不关心其他任何行为

- View：用户操作界面。当 ViewModel 对 Model 进行更新的时候，会通过数据绑定更新到 View

- ViewModel：业务逻辑层，View 需要什么数据，ViewModel 要提供这个数据；

  View 有某些操作，ViewModel 就要响应这些操作

MVVM 模式简化了界面于业务的依赖，解决了数据频繁更新

MVVM 利用数据的双向绑定

使得 Model 变化时 ViewModel 会自动更新

而 ViewModel 变化时，View 也会自动变化

<!--more-->

## Vue 生命周期

> 总共分为 8 个阶段 创建前/后 载入前/后 更新前/后 销毁前/后

各个生命周期的作用

| 生命周期      | 描述                                                                   |
| ------------- | ---------------------------------------------------------------------- |
| beforeCreate  | 组件实例被创建之初，组件的属性生效之前                                 |
| created       | 组件实例已经完全创建，属性也绑定，但真实 dom 还没有生成，\$el 还不可用 |
| beforeMount   | 在挂载开始之前被调用：相关的 render 函数首次被调用                     |
| mounted       | el 被新创建的 vm.\$el 替换，并挂载到实例上去之后调用该钩子             |
| beforeUpdate  | 组件数据更新之前调用，发生在虚拟 DOM 打补丁之前                        |
| update        | 组件数据更新之后                                                       |
| activited     | keep-alive 专属，组件被激活时调用                                      |
| deadctivated  | keep-alive 专属，组件被销毁时调用                                      |
| beforeDestory | 组件销毁前调用                                                         |
| destoryed     | 组件销毁后调用                                                         |

<br>

<img src="http://poetries1.gitee.io/img-repo/2020/07/61.png">

<br>

## Vue2.x 实现数据双向绑定的原理

Object.defineProperty 数据劫持 + 发布者订阅者模式

## Vue 组件间的参数传递

父组件与子组件传值

- 父组件传给子组件：子组件通过 props 方法接受数据

- 子组件传给父组件：子组件通过$emit 自定义事件

## 5.Vue 的路由实现：hash 模式和 history 模式

- hash 模式：# 用 window.location.hash 读取

- history 模式： pushState 和 replaceState 是一个栈操作

## Vuex

- 只用来读取的状态集中放在 store 中

- 改变状态的方式是提交 mutations，这是个同步的事物；异步逻辑应该封装在 action 中

- state：Vuex 使用单一状态树,即每个应用将仅仅包含一个 store 实例，但单一状态树和模块化并不冲突。

  存放的数据状态，不可以直接修改里面的数据。

- mutations：mutations 定义的方法动态修改 Vuex 的 store 中的状态或数据

- getters：类似 vue 的计算属性，主要用来过滤一些数据。

- action：actions 可以理解为通过将 mutations 里面处里数据的方法变成可异步的处理数据的方法，简单的说就是异步操作数据。

  view 层通过 store.dispath 来分发 action

## v-if 和 v-show 区别

- v-if 按照条件是否渲染 （不一定渲染）

- v-show 是 display 的 block 或 none （一定渲染）

## $route和$router 的区别

- $router 是路由实例 包括了路由的跳转方法，钩子函数等

- $route 是路由信息对象，包括 path、params 等

## 如何让 CSS 只在当前组件起作用

组件内的 style 标签添加 scoped 如果不添加 则默认为全局样式

## scoped 样式穿透

> scoped 虽然避免了组件之间的样式污染，但是很多时候我们需要修改组件中的某个样式，但是又不想去除 scoped 属性

尤其是在使用第三方的 UI 库修改其样式时

解决：

- 使用/deep/

## `<keep-alive>`的作用

> keep-alive 可以实现组件缓存，当组件切换时不会对当前组件进行卸载

- `<keep-alive></keep-alive>` 包裹动态组件时，会缓存不活动的组件实例,主要用于保留组件状态或避免重新渲染

> 比如有一个列表和一个详情，那么用户就会经常执行打开详情=>返回列表=>打开详情…这样的话列表和详情都是一个频率很高的页面，

那么就可以对列表组件使用`<keep-alive></keep-alive>`进行缓存，这样用户每次返回列表的时候，都能从缓存中快速渲染，而不是重新渲染

- 常用的两个属性`include/exclude`，允许组件有条件的进行缓存

- 两个生命周期`activated/deactivated`，用来得知当前组件是否处于活跃状态

## 在 Vue 中使用插件的步骤

- import from 导入该插件

- Vue.use(xxx)

  该方法会在内部调用插件的 install 方法

## Vue 组件中的 data 为什么必须是函数

- 每个组件都是 Vue 的实例

- 组件共享 data 属性，当 data 的值是一个引用类型时，改变其中一个会影响其他

## vue-router 有哪几种导航守卫

> 全局前置守卫 router.beforeEach

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

- `to: Route`: 即将要进入的目标（路由对象）

- `from: Route`: 当前导航正要离开的路由

- `next: Function`: 一定要调用该方法来 `resolve` 这个钩子。（一定要用这个函数才能去到下一个路由，如果不用就拦截）

- 执行效果依赖 next 方法的调用参数。

- `next()`: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。

- `next(false)`:取消进入路由，url 地址重置为 from 路由地址(也就是将要离开的路由地址)

> 全局解析守卫

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

> 全局后置钩子

你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 next 函数也不会改变导航本身：

```js
router.afterEach((to, from) => {
  // ...
});
```

> 路由独享的守卫

你可以在路由配置上直接定义 beforeEnter 守卫：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      },
    },
  ],
});
```

> 组件内的守卫

- beforeRouteEnter

- beforeRouteUpdate (2.2 新增)

- beforeRouteLeave

```js
const Foo = {
  template: `...`,
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  },
};
```

## watch vs computed

**computed:**

- computed 是计算属性,也就是计算值,它更多用于计算值的场景

- computed 具有缓存性,computed 的值在 getter 执行后是会缓存的，只有在它依赖的属性值改变之后，

  下一次获取 computed 的值时才会重新调用对应的 getter 来计算 computed 适用于计算比较消耗性能的计算场景

**watch:**

- 更多的是「观察」的作用,类似于某些数据的监听回调,用于观察 props $emit 或者本组件的值,

  当数据变化时来执行回调进行后续操作

- 无缓存性，页面重新渲染时值不变化也会执行

**小结:**

- 当我们要进行数值计算,而且依赖于其他数据，那么把这个数据设计为 computed

- 如果你需要在某个数据变化时做一些事情，使用 watch 来观察这个数据变化

## ref 的作用

- 获取 dom 元素`this.$refs.xxx`

- 获取子组件中的数据

- 调用子组件中的方法

## Vue 中的 key 有什么用

- key 是 Vue 中的 vnode 标记的唯一 id，通过这个 key，我们可以使 diff 操作更加准确，更快速

## v-model 的原理

可以看成是 input 和 value 的语法糖

- :value 动态的将 data 绑定给 input

- @input 一旦有输入 就修改 data 中的数据

## v-if 和 v-for 为什么不建议连用

v-for 会优于 v-if 先执行

如果此时 v-if 的执行结果是 false 就会造成性能浪费

可以用嵌套的标签 在大盒子上使用 v-if

## Vue 改变数组触发试图更新

调用方法：Vue.set(target,key.value)

target 可以是对象或数组
