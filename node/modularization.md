---
title: Node 模块化
date: 2021-03-09
tags:
  - Node
categories:
  - Node
---


node模块化采用commonjs的方式 即 require / module.export 的形式

## module.export === export ？

我们直接输出 module.exports === exports

会发现两者是相等的 所以exports实际上是module.exports的引用

而require引用的永远是module.exports

也就是说对于exports只能使用exports.xxx = xxx 的形式赋值

因为一旦修改了exports的指向 那么它最终将不会被require引用

而module.exports既可以使用module.exports.xxx = xxx

也可以使用module.exports = xxx

<!-- more -->

## 循环引用

TODO

## 模块导入

### 有后缀名

### 无后缀名

按如下循序查找

- 把x当作文件

  - x

  - x.js

  - x.json

  - x.node

- 把x当作文件夹

  - x/index.js

  - x/index.json

  - x/index.node