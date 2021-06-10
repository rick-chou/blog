# bind-call-apply

## 手写call

### 原生call

我们首先来看看call方法实现了什么

```javascript
var foo = {
  name: 'chou',
}

function sayName() {
  console.log(this.name)
}

sayName.call(foo) // chou
```

不难看出

* call改变了this的指向，指向到foo对象
* sayName方法执行了

### 实现

```javascript
var foo = {
  name: 'chou',
}

function sayName() {
  console.log(this.name)
}

------------------------
// 改造foo对象
var foo = {
  name: 'chou',
  sayName() {
    console.log(this.name)
  }
}

foo.sayName() // chou
```

所以 如果我们

* 将函数设置为对象的属性
* 执行该函数
* 删除该函数

好啦 动手实现吧

```javascript
Function.prototype._call = function (context) {
  context.fn = this
  context.fn()
  delete context.fn
}
```

但是call的第二个参数可以接收一个变量

同时 如果不传递参数 默认会绑定window

好啦 我们继续模拟

```javascript
Function.prototype._call = function (context) {
  var args = []
  // 从第二个参数开始 因为第一个存放的是我们的函数
  for (let i = 1; i < arguments.length; i++) {
    args.push(arguments[i])
  }
  context = context || window
  context.fn = this
  context.fn(...args)
  delete context.fn
}
```

## 手写apply

### 思路

apply和call的区别在于 apply 的参数接收一个数组参数 而 call 则是参数列表

我们可以取argument的第一个元素 因为如果正确的传递 则argument的第一个元素应该是我们想要的 那个作为参数的数组

我们可以对这个值进行校验

* 如果不是数组 则抛出异常
* 如果不传 则默认只调用该方法
* 如果是数组 则按正确的处理

### 实现

```javascript
Function.prototype._apply = function (context) {
  var args = arguments[1]
  context = context || window
  context.fn = this
  if (!args) {
    context.fn()
  } else {
    if (args instanceof Array) {
      context.fn(...args)
    } else {
      try {
        throw new Error('CreateListFromArrayLike called on non-object')
      } catch (e) {
        console.log(e)
      }
    }
  }
  delete context.fn
}
```

## 手写bind

### 第一版

我们借用 apply 或 call 返回一个新的函数即可

```javascript
Function.prototype._bind = function (context) {
    var self = this;
    return function () {
        return self.apply(context);
    }

}
```

### 第二版

我们模拟bind调用中可以传递参数的形式

```javascript
Function.prototype._bind = function (context) {

    var self = this;
    // 获取第二个参数到最后一个参数 第一个为函数 我们需要传入的变量
    var args = Array.prototype.slice.call(arguments, 1);

    return function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        // 用concat合并两次的参数
        return self.apply(context, args.concat(bindArgs));
    }

}
```

### 第三版

bind绑定的函数 还能用 new 关键字 来创建对象

TODO 此部分探索中

