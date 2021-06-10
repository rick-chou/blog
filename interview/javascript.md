## 1.异步

JS 语言是单线程的

JS 需要异步的根本原因是 JS 是单线程运行的，即在同一时间只能做一件事，不能“一心二用”。

> 一个 Ajax 请求由于网络比较慢，请求需要 5 秒钟。
> 如果是同步，这 5 秒钟页面就卡死在这里啥也干不了了。
> 异步的话，就好很多了，5 秒等待就等待了，其他事情不耽误做，至于那 5 秒钟等待是网速太慢，不是因为 JS 的原因。

```js
var a = true;
setTimeout(function () {
  a = false;
}, 100);
while (a) {
  console.log('while执行了');
}
```

以上代码的执行结果不是 100ms 后 a 为 false 所以打断 while 循环

事实上 当程序进入 while 循环后 就一直是死循环了

因为 JS 是单线程语言 所以无法执行到定时器

<!--more-->

## 2.Set && Map

Set 和 Map 实例都不允许元素有重复

Set 的 key 和 value 一样 类似于数组

Map 的 key 和 value 可以设置 类似于对象

Set 实例的属性和方法有

- size：获取元素数量。

- add(value)：添加元素，返回 Set 实例本身。

- delete(value)：删除元素，返回一个布尔值，表示删除是否成功。

- has(value)：返回一个布尔值，表示该值是否是 Set 实例的元素。

- clear()：清除所有元素，没有返回值。

Set 实例的遍历，可使用如下方法

- keys()：返回键名的遍历器。

- values()：返回键值的遍历器。不过由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），

  所以 keys()和 values()返回结果一致。

- entries()：返回键值对的遍历器。

- forEach()：使用回调函数遍历每个成员。

Map 实例的属性和方法如下：

- size：获取成员的数量

- set：设置成员 key 和 value

- get：获取成员属性值

- has：判断成员是否存在

- delete：删除成员

- clear：清空所有

Map 实例的遍历方法有：

- keys()：返回键名的遍历器。

- values()：返回键值的遍历器。

- entries()：返回所有成员的遍历器。

- forEach()：遍历 Map 的所有成员。

## 3.XMLHttpRequest

```js
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = () => {
  if (xhr.readyState == 4) {
    if (xhr.status == 200) {
      alert(xhr.responseText);
    }
  }
};
xhr.open('GET', '/api', false);
xhr.send(null);
```

xhr.readyState 的状态码说明：

- 0 代理被创建，但尚未调用 open() 方法。

- 1 open() 方法已经被调用。

- 2 send() 方法已经被调用，并且头部和状态已经可获得。

- 3 下载中， responseText 属性已经包含部分数据。

- 4 下载操作已完成

xhr.status 即 HTTP 状态码，有 2xx 3xx 4xx 5xx 这几种，比较常用的有以下几种：

- 200 正常

- 3xx

  - 301 永久重定向

  - 302 临时重定向。临时的，不是永久的

  - 304 资源找到但是不符合请求条件，不会返回任何主体。

    如发送 GET 请求时，head 中有 If-Modified-Since: xxx（要求返回更新时间是 xxx 时间之后的资源），

    如果此时服务器 端资源未更新，则会返回 304，即不符合要求

- 404 找不到资源

- 5xx 服务器端出错了

## 4.Fetch

更简洁的 ajax 并且支持 Promise 的回调

```js
// 拿到一个本地json文件的内容
fetch('./demo.json')
  .then((res) => res.json())
  .then((res) => {
    console.log(res);
  });
```

## 5.闭包

函数 A 中有函数 B

函数 B 中使用了函数 A 的变量

函数 B 就被称为闭包

## setTimeout

承接以上闭包的问题

用定时器的第三个问题可以解决一个老生常谈的问题

```js
for (var i = 0; i < 10; i++) {
  setTimeout(
    (j) => {
      console.log(j);
    },
    100,
    i
  );
}
// 打印 0 - 9
```

## 6.深拷贝

JSON.parse(JSON.stringify(obj))

- 会忽略 undefined

- 不能序列化函数

- 不能解决循环引用的对象

Object.assign({}, obj)

- 浅拷贝 只完成了属性的深拷贝

## 7.防抖 && 节流

## 8.call && apply && bind

- bind 返回一个改变 this 的函数 强绑定 无法再修改内部 this 的指向

- apply 立即执行该函数 接受一个参数数组

- call 立即执行该函数 接受一个参数列表

## 10.事件的三个阶段

捕获 - 目标 - 冒泡

可以用 addEventListener 的第三个参数来决定是捕获还是冒泡

默认是 false 冒泡 改为 true 则为捕获

事件代理

思路：

如果是动态生成的元素 可以考虑在父元素上绑定事件

利用事件冒泡 在父元素处触发事件

## 11.跨域

JSONP

- 利用 script 标签不受跨域限制

- 仅限于 get 请求

document.domain

- 适用于二级域名相同的情况

  如 a.chou.com && b.chou.com

## 12.Event Loop

执行顺序：

同步 > 微任务 > 宏任务

微任务

- process.nextTick

- Promise

- Object.observe

- MutationObserver

宏任务

- script

- setTimeout

- setInterval

- I/O

- UI rendering

所以页面的 script 标签要写在页面的最下面 防止页面阻塞

## 13.浏览器渲染机制

- 处理 HTML 并构建 DOM 树

- 处理 CSS 并构建 CSSOM 树

- 将 DOM 树和 CSSOM 树合并成一棵渲染树

- 根据渲染树来布局 计算每一个节点的位置

- 调用 GPU 绘制 合成图层 显示在屏幕上

## 14.重绘 && 回流

重绘：不改变外观 只改变例如背景色 透明度等

回流：布局或几何属性需要改变
