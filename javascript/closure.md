## 闭包的形成条件

- 函数嵌套

- 内部函数引用外部函数的局部变量

## 闭包的缺点

- 容易造成内存泄漏 （现代浏览器性能？？？）

## 闭包的优点

- 延长外部函数局部变量的生命周期

## 什么是闭包

- 密闭的容器 存储数据

- 闭包是一个对象 键值对

## case1

```js
function outer() {
  var a = 123;
  function inner() {
    console.log(a);
  }
  return inner;
}

foo()(); //123
```

又上述代码可知

闭包延长了变量 a 的生命周期 让外部作用域有能力获取到函数内部的变量

闭包就是将函数内部和函数外部连接起来的一座桥梁

## case2

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, 1000);
}
```

上述代码想实现 每秒输出一个数字 从 1 - 5

但是事实上每秒输出一个 6

因为 setTimeout 是异步队列 执行时 for 循环里 i 的值已经变成了出循环的值

解决方法

```js
for (var i = 1; i <= 5; i++) {
  (function fn(i) {
    setTimeout(function timer() {
      console.log(i);
    }, 1000);
  })(i);
}
```

因为函数是一个独立的作用域 可以记录每一个 i 的值 拿到属于自己的 a
