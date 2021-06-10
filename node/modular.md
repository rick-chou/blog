node 模块化采用 commonjs 的方式 即 require / module.export 的形式

## module.export === export ？

我们直接输出 module.exports === exports

会发现两者是相等的 所以 exports 实际上是 module.exports 的引用

而 require 引用的永远是 module.exports

也就是说对于 exports 只能使用 exports.xxx = xxx 的形式赋值

因为一旦修改了 exports 的指向 那么它最终将不会被 require 引用

而 module.exports 既可以使用 module.exports.xxx = xxx

也可以使用 module.exports = xxx

## 循环引用

TODO

## 模块导入

### 有后缀名

### 无后缀名

按如下循序查找

- 把 x 当作文件

  - x

  - x.js

  - x.json

  - x.node

- 把 x 当作文件夹

  - x/index.js

  - x/index.json

  - x/index.node