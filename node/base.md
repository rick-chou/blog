## 什么是 node

node 是 js 的运行环境

## 两种运行模式

### 交互模式

在命令行输入 node 回车 即可进入交互模式

使用.help 可以查看一些帮助信息 使用 tab 可以自动补全命令

连按两次 tab 可以查看所有命令

例如输入 Math. 然后连续按两次 tab 即可查看所有 Math 下的命令

推出使用.exit 或者连续两次 ctrl+c

### 脚本模式

直接使用在命令行 输入 node 文件名 即可执行对应的 js

## 全局对象

node 的全局对象是 global 与 js 的 window 不一样

在交互模式下 声明的全局变量都可以用 global 访问到

但是在脚本模式下 访问不到

例如

```js
var a = 1;
b = 2;
console.log(global.a, global.b);
```

以上代码在交互模式下会输出 1 和 2 但是在脚本模式下都都为 undefined

## 全局函数

js 中的全局函数 在 node 下全部都可以使用

除此之外 node 还新增了 setImmediate / clearImmediate

通过打印 global 全局对象 我们就可以看到全部的全局函数

```js
{
  global: [Circular *1],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  queueMicrotask: [Function: queueMicrotask],
  performance: [Getter/Setter],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  }
}
```

setImmediate 在任务队列的顶部 所有它会优先于 setInterval 等这些任务队列中的函数先执行

作为比较的是 process.nextTick

这个函数位于主程序的队尾 所以它会优先与 setImmediate 先执行

<img src = "https://gitee.com/LuckyChou/blog-images/raw/master/node/async.png"/>

```js
setTimeout(() => {
  console.log('setTimeout执行了');
}, 0);

setImmediate(() => {
  console.log('setImmediate执行了');
});

process.nextTick(() => {
  console.log('nextTick执行了');
});

console.log('同步函数执行了');
```

以上代码的执行结果为

```text
同步函数执行了
nextTick执行了
setTimeout执行了
setImmediate执行了
```

## 内置模块

### console

```js
/**
 * console.time()
 * console.timeEnd()
 * 获取程序运行的时间
 */
console.time('for');

for (let i = 0; i <= 10000000; i++) {}

console.timeEnd('for');

console.time('while');

let i = 0;
while (i <= 10000000) {
  i++;
}

console.timeEnd('while');

/**
 * console.table
 * 表格形式输出一个对象
 * 可以传入一个对象
 * 也可以传入一个数组 此时第二个参数为一个筛选项
 */

const obj1 = {
  name: 'chou',
  age: 18,
  hobby: 'ramires',
};

console.table(obj1);

const obj2 = {
  name: 'ramires',
  age: 18,
  hobby: 'chou',
};

console.table([obj1, obj2]);
console.table([obj1, obj2], ['name']);
```

### fs

```js
const fs = require('fs');

// 清空写入
fs.writeFile('./note.txt', 'hello\n', (err) => {
  console.log('写入成功');
});

// 删除
fs.unlink('./note.txt', () => {
  console.log('删除成功');
});

// 追加写入
fs.appendFile('./note.txt', 'hello\n', (err) => {
  console.log('追加成功');
});

// 读取文件内容
fs.readFile('./note.txt', (err, data) => {
  console.log(data.toString());
});

// 新建文件夹
fs.mkdir('./demo', (err) => {
  console.log(err);
});

// 删除文件夹
fs.rmdir('./demo', (err) => {
  console.log('删除成功');
});

// 读取文件夹
fs.readdir('../', (err, dir) => {
  console.log(dir);
});
```

### path

```js
const path = require('path');

console.log(__dirname);
console.log(__filename);

// 获取扩展名
console.log(path.extname(__filename));

// 拼接路径
console.log(path.join(__dirname, '/index.js'));
```

### http

```js
const http = require('http');
const server = http.createServer((req, res) => {
  console.log(req);
  res.end('hello');
});

server.listen(3000, () => {
  console.log('服务器启动成功');
});
```

### process

```js
console.log(process.arch);
console.log(process.platform);

console.log(process.argv);

console.log(process.cwd());

process.nextTick(() => {
  console.log('hello');
});
```
