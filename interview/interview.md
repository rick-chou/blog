## 原型链

原型 prototype

给其他对象提供共享属性的方法

构造函数的 prototype 指向原型对象 原型对象的 constructor 指向构造函数

实例对象的`__proto__`指向原型对象

原型对象的`__proto__`指向 Object 的原型对象

Object 的原型对象的`__proto__`指向 null

Object 的 prototype 指向 Object 的原型对象

Object 的原型对象的 constructor 指向 Object

访问一个对象的属性时 就会沿着原型链一层一层往上找

若到 Object.prototype 还未找到 就返回 undefined

<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/context/context11.png" />

## MVVM

M Model 数据层

V View 视图层

VM View-Model 业务逻辑层

## 从输入 url 到页面经历了什么

- DNS 解析 将域名解析成 IP 地址

- TCP 连接 三次握手

- 发送 HTTP 请求

- 服务器响应 HTTP 请求并返回响应报文

- 浏览器解析渲染页面

- 断开连接 TCP 四次挥手

三次握手

浏览器告诉服务器我要发送请求了 请你准备好

服务器响应浏览器 我准备好了 你发送请求吧

浏览器告诉服务器我马上就发送了 准备接受吧

四次挥手

浏览器告诉服务器 我请求报文发送完毕了 你关闭吧

服务器告诉浏览器 我请求报文接受完毕了 我要关闭了

服务器告诉浏览器 我响应报文发送完毕了 你关闭吧

浏览器告诉服务器 我响应报文接受完毕了 我要关闭了

## webpack loader 和 plugin 的区别

- Loader 只负责单纯的文件转换

- Plugin 丰富了 webpack 的功能 比如压缩代码

## style-loader 和 css-loader 的加载顺序

loader 的加载顺序是从右往左

这里的编译顺序是先用 css-loader 将 css 代码编译

再交给 style-loader 插入到网页里面去

所以 css-loader 在右，style-loader 在左

## Promise 的方法

- Promise.all 全部都成功时

- Promise.allSettled 全部都结束时

- Promise.any 有一个返回 fulfilled

- Promise.race 返回第一个 fulfilled 或 rejected

- Promise.reject

- Promise.resolve

## http 强缓存 协商缓存

- 强缓存 Expires Cache-control

- 协商缓存 ETag last-modifed

### Expires vs Cache-Control

**Expires**

Response header 里的过期时间 浏览器再次加载时 如果在这个时间内 就命中强缓存

**Cache-Control**

当设置 max-age = 300 表示 在接下来的五分钟内再次请求 就会命中强缓存

Expires 是 Http1.0 的产物 Cache-Control 是 1.1 的产物

两者同时存在的话 后者优先级更高

Expires 是过时的产物 只是为了兼容

对比 Expires Cache-Control 的时间段 控制起来更加容易

### ETag Last-Modified

**ETag**

上一次加载资源时 response header 的一个唯一标识 一旦资源有变化 ETag 就会重新生成

浏览器下一次向服务器发送请求时 会将上一次 response header 的 Etag 放入 request header 的 If-None-Match 中

服务器会进行对比 如果相等 就命中协商缓存

**Last-Modified**

是该资源文件最后一次修改的时间 会在上一次 response header 中返回

浏览器会在下一次 request header 中 放在 If- Modified-Since 中带上

服务器会进行对比 如果相等 就命中协商缓存

Etag 比后者优先级更高

但是性能比不上后者 因为每次变更 Etag 都要重新生成 但是后者只是记录一个时间

精度上 Etag 也比后者更好

## New 内部实现了什么

在内存中创建一个新的对象

构造函数内部的 this 指向这个对象

将这个对象的 prototype 指向构造函数的 prototype 这样这个对象就拥有构造函数中的方法

执行构造函数中的代码

## 事件委托

事件的三个阶段

捕获 -> 目标 -> 冒泡

事件委托就是利用冒泡 将子元素的事件通过事件冒泡在父元素触发事件

可以通过 addEventListener 的第三个参数来决定是捕获还是冒泡

true 事件捕获

false （默认）事件冒泡

阻止冒泡

stopPropagation

IE cancleBubble

## Vue 生命周期

- beforeCreate

- created 最早这里有 $data 可以在这里发送网络请求

- beforeMount

- mounted 最早这里有 $el 可以在这里操作 dom

- beforeUpdate

- updated

- beforeDestroy

- destroyed

keep-alive 专属

- activated

- deactivated

## 闭包

内部函数引用了外部函数的变量

可以延长外部函数变量的作用域

## 单向数据流和双向数据流在编码中的体现

### 单向数据流

从组件角度来看 数据从组件树由上往下流动 即只从父组件流向子组件

这样的好处是防止子组件意外的修改了父组件的状态

对于 Vuex 这些状态管理工具来说

数据流的流向也是单向的

即组件发送 dispatch/commit -> store -> view

总的来说 单向数据流的好处是使状态变得可记录 可追踪

### 双向数据绑定

指 view 的改变会改变 modal

Modal 的改变会改变 view

这样的好处是省事方便 省去了很多业务代码

但是一但出错 紊乱的数据流会变得难以追踪和定位

## Vue 实现数据双向绑定的原理

### Object.defineProperty

Object.key 可以获取对象的所有属性

```js
Object.defineProperty(obj, attr, {
  value 不能和 set / get 一起使用
  writable 修改 默认 false
  enumerable 枚举 默认 false
  onfigurable 删除 默认 fslse
  set
  get
})
```

### Proxy

```js
new Proxy(obj, handler);

const hander = {
  set(obj, prop, value) {
    obj[prop] = value;
    console.log(`${prop}属性的set方法执行了`);
  },
  get(obj, prop) {
    console.log(`${prop}属性的get方法执行了`);
    return obj[prop];
  },
};
```

## 实现继承的几种方式

```js
// 定义一个父类
function Father(name) {
  // 属性
  this.name = name || '->father';
  // 实例方法
  this.sayName = function () {
    console.log(this.name);
  };
}
// 原型方法
Father.prototype.age = 18;
Father.prototype.sayAge = function () {
  console.log(this.age);
};

console.log('----------原型链继承----------');
function Son(name) {
  this.name = name || '->son';
}
Son.prototype = new Father();
var s = new Son('son');
console.log(s.name); // son
s.sayAge(); // 18
s.sayName(); // son
console.log(s.age); // 18
console.log(s instanceof Father); // true
console.log(s instanceof Son); // true

console.log('----------构造函数继承----------');
function Son(name) {
  Father.call(this);
  this.name = name || '->son';
}
var s = new Son('son');
console.log(s.name); // son
//s.sayAge(); // 抛出错误
s.sayName(); // son
console.log(s.age); // undefined
console.log(s instanceof Father); // false
console.log(s instanceof Son); // true

console.log('----------实例继承----------');
function Son(name) {
  var f = new Father();
  f.name = name || '->son';
  return f;
}
var s = new Son('son');
console.log(s.name); // son
s.sayAge(); // 18
s.sayName(); // son
console.log(s.age); // 18
console.log(s instanceof Father); // true
console.log(s instanceof Son); // false

console.log('----------组合继承----------');
function Son(name) {
  Father.call(this);
  this.name = name || '->son';
}
Son.prototype = new Father();
// 修复构造函数指向
Son.prototype.constructor = Son;
var s = new Son('son');
console.log(s.name); // son
s.sayAge(); // 18
s.sayName(); // son
console.log(s.age); // 18
console.log(s instanceof Father); // true
console.log(s instanceof Son); // true

console.log('----------寄生组合继承----------');
function Son(name) {
  Father.call(this);
  this.name = name || '->son';
}
(function () {
  // 创建一个没有实例方法的类
  var None = function () {};
  None.prototype = Father.prototype;
  // 将实例作为子类的原型
  Son.prototype = new None();
  // 修复构造函数指向
  Son.prototype.constructor = Son;
})();
var s = new Son('son');
console.log(s.name); // son
s.sayAge(); // 18
s.sayName(); // son
console.log(s.age); // 18
console.log(s instanceof Father); // true
console.log(s instanceof Son); // true
```

## $nextTick

## React 生命周期

### 挂载

- constructor()

- static getDerivedStateFromProps()

- render()

- componentDidMount()

### 更新

- static getDerivedStateFromProps()

- shouldComponentUpdate()

- render()

- getSnapshotBeforeUpdate()

- componentDidUpdate()

## 卸载

- componentWillUnmount()

## Hook 中怎么模拟类组件的生命周期

- 第一个参数 函数形式 可实现等同于 componentDidMount shouldComponentUpdate componentWillUnmount

- 并且可以返回一个函数 用来消除副作用 类似 componentWillUnmount 可以做一些事件的解绑 定时器的关闭等

- 第二个参数 数组 状态依赖项 实现性能优化 如果传[] 则等同于不开启 shouldComponentUpdate

## 如何提升 webpack 的打包速度
