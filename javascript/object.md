---
title: JavaScript Object
date: 2021-02-06
tags:
  - JS
categories:
  - JS
---

JS 中除了基础类型 就是 Object 类型 近期也打算再学习一下 Vue 的响应式原理

无论是 Vue2 的 defineProperty 还是 Vue3 的 Proxy 都是建立在 Object 的 api 上的

所以本篇主要是盘点一下 Object 的 api 更详细的文档请移步<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object">MDN</a>

### 创建

- 对象字面量

```js
const obj = {
  name: 'chou',
  age: 18,
};
```

- Object.create()

使用现有的对象来提供新创建的对象的**proto**

- new Object()

### 遍历

#### Object.keys(obj)

返回一个包含所有给定对象自身可枚举属性名称的数组

#### Object.entries(obj)

返回给定对象自身可枚举属性的 [key, value] 数组

#### Object.values(obj)

返回给定对象自身可枚举值的数组

#### Object.getOwnPropertyDescriptor(obj)

获取一个属性的 descriptor （在 defineProperty 中声明的 未声明的不会返回）

#### Object.getOwnPropertyNames(obj)

返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括 Symbol 值作为名称的属性）组成的数组

#### Object.getOwnPropertySymbols(obj)

返回一个给定对象自身的所有 Symbol 属性的数组

#### Object.getPrototypeOf(obj)

返回指定对象的原型

#### for...in

### 删除

- delete(obj.props)

### 扩充

#### Object.defineProperty(obj)

#### Object.defineProperties(obj)

- obj: 要在其上定义属性的对象。

- prop: 要定义或修改的属性的名称。

- descriptor: 将被定义或修改的属性的描述符。

  - value: 直接给这个属性赋值

  - writable: 是否可修改 默认 false

  - enumerable: 是否可枚举(遍历拿值) 默认 false

  - configurable: 是否可删除 默认 false

  - set

  - get

#### Object.preventExtensions(obj)

让一个对象变的不可扩展，也就是永远不能再添加新的属性

#### Object.isExtensible(obj)

判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）

#### Object.freeze(obj)

冻结一个对象 被冻结后不能进行任何操作

不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。

此外，冻结一个对象后该对象的原型也不能被修改。

#### Object.isFrozen(obj)

判断一个对象是否被冻结

#### Object.seal(obj)

封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置

#### Object.isSealed(obj)

判断一个对象是否被密封

#### Object.setPrototypeOf(obj, prototype)

设置一个指定的对象的原型

> 警告: 由于现代 JavaScript 引擎优化属性访问所带来的特性的关系，更改对象的 [[Prototype]]在各个浏览器和 JavaScript 引擎上都是一个很慢的操作。其在更改继承的性能上的影响是微妙而又广泛的，这不仅仅限于 obj.**proto** = ... 语句上的时间花费，而且可能会延伸到任何代码，那些可以访问任何[[Prototype]]已被更改的对象的代码。如果你关心性能，你应该避免设置一个对象的 [[Prototype]]。相反，你应该使用 Object.create()来创建带有你想要的[[Prototype]]的新对象。

#### Object.getPrototypeOf(obj)

获取一个指定的对象的原型

### 复制

#### Object.assign(target, ...sources)

将所有**可枚举属性**的值从一个或多个源对象分配到目标对象。它将返回目标对象。

**只进行第一层的深拷贝**

### 其他

#### Object.is(val1, val2)

与 === 运算符基本一致

区别在于 === 将 -0 和 +0 当作一样 而将 NaN 和 NaN 当作不一样
