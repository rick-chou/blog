---
title: JavaScript 轮询机制
date: 2020-06-16
tags:
 - JavaScript
categories:
 - JS篇
---


## JavaScript轮询机制


首先，我们来分析一段代码


```js
console.log('******************** start *******************')

setTimeout(() => {
  console.log('setTimeout')
}, 0)

new Promise((resolve, reject) => {
  for (var i = 0; i < 5; i++) {
    console.log(i)
  }
  resolve()
}).then(() => {
  console.log('Promise Success')
})

console.log('******************** end *******************')
```

<!--more-->

好了 上面程序的执行结果是



```js
******************** start *******************
0
1
2
3
4
******************** end *******************
Promise Success
setTimeout
```



而且不管定时器和promise语句的顺序如何 执行结果都不会发生变化

为什么会有这种现象呢



### 引入：

- Event Loop

- Task  /  MicroTask



#### Event Loop

Event Loop就是事件循环，我们都知道JavaScript是一门单线程语言，而例如Ajax请求都是耗时的操作，为了阻止页面冻结的情况，JavaScript有异步的解决方案，而Event Loop就是一种解决JavaScript单线程运行时不会阻塞的一种机制，也就是上面提到异步的原理。



#### Task

Task队列又称宏任务队列，宏任务队列可以有多个，所有的JavaScript主线程上的代码都在**第一个**宏任务队列上

宏任务队列主要是：整体代码`script`，`setTimeout`，`setInterval`、`I/O`、`UI render`



#### MicroTask

MicroTask队列又称微任务队列，注意，和宏任务队列不同的是，微任务队列只有一个。

微任务队列主要是：`Promise`、`Object.observe`、`MutationObserver`



有了这些知识，接下来就可以正式讲讲JS轮询机制了



------



### JS轮询机制

JS的执行机制(一)：

1. 首先判断JS是同步还是异步,同步就进入主进程,异步就进入event table

2. 异步任务在event table中注册函数,当满足触发条件后,被推入event queue

3. 同步任务进入主线程后一直执行,直到主线程空闲时,才会去event queue中查看是否有可执行的异步任务,如果有就推入主进程中（所以回调函数也被称为钩子函数）

   

JS的执行机制(二)

1. 执行一个宏任务,过程中如果遇到微任务,就将其放到微任务的【事件队列】里
2. 当前宏任务执行完成后,会查看微任务的【事件队列】,并将里面全部的微任务依次执行完



<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/event.png" alt="Event Table" style="zoom:80%;" />



<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/event%20table.png" style="zoom:80%;" />





好了，最后我们再分析一下一开始的那段代码



第一个宏任务队列：

```
console.log('******************** start *******************')

for (var i = 0; i < 5; i++) {
    console.log(i)
  }

console.log('******************** end *******************')
```



第二个宏任务队列

```
setTimeout(() => {
  console.log('setTimeout')
}, 0)
```



微任务队列（注：只有一个）

```
.then(() => {
  console.log('Promise Success')
})
```

