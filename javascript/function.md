---
title: JavaScript Function
date: 2020-10-25
tags:
 - JavaScript
categories:
 - JS篇
---
## Function

- 函数是一等公民

  - 由于函数与其他数据类型地位平等，所以在 js 中又称函数为第一公民

- function 声明的函数存在变量提升

  - function foo() {......}

- 函数表达式不存在变量提升

  - var foo = function() {......}

<!-- more -->

### 函数的属性和方法

name 返回函数的名字

length 返回函数预计参数的个数

```js
const foo = function (name) {};

console.log(foo.name);  // foo
console.log(foo.length); // 1
```

### 函数作用域

ES5 中 js 只有两种作用域

一种是全局作用域

一种是函数作用域 所以函数拥有自己的作用域

- 函数内部的变量提升

  - 声明的变量会提升到所在作用域的最顶部

  - 函数优先

```js
// 代码块
printf()
function printf() {
  console.log(a)
  var a = 123
}

// 解析成
function printf() {
  var a = undefined
  console.log(a)
  a = 123
}
printf()
```

- 函数外部无法访问函数内部的变量

### 参数

- 同名参数 则取后面那个 （ES6 不允许有同名参数）

- arguments 对象

  - 包含函数运行时所有的参数

  - 伪数组 不是真正的数组

  - `arguments.length`可以获取参数的个数

### 闭包

闭包就是将函数内部和函数外部连接起来的一座桥梁

- 可以延长变量的作用域

### 立即调用函数（IIFE）

- 函数是一个独立的作用域 可以解决多个 js 文件之间变量名冲突的问题

  - 注意

    - 结尾有；

- 形式

  ```js
  //形式一
  ;(function() {})()

  //形式二
  ;(function() {})()
  ```