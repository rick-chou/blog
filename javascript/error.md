---
title: JavaScript 常见错误类
date: 2020-10-25
tags:
 - JavaScript
categories:
 - JS篇
---


## 背景

最近 在做JS笔试题的时候 一直分不清楚每个错误类 所以在查阅资料后 写下了这篇笔记

<!--more-->

## SyntaxError

SyntaxError是解析代码时发生的语法错误

```js
// 变量名错误 
var 1a; 

// 缺少括号 
console.log 'hello');
```

## ReferenceError

ReferenceError是引用一个不存在的变量时发生的错误

```js
// 比如我们直接打印一个未声明的变量
console.log(unknownVariable) // ReferenceError: unknownVariable is not defined

// 另一种触发场景是，将一个值分配给无法分配的对象，比如对函数的运行结果或者this赋值。

console.log() = 1 // ReferenceError: Invalid left-hand side in assignment 

this = 1 // ReferenceError: Invalid left-hand side in assignment

// 上面代码对函数console.log的运行结果和this赋值，结果都引发了ReferenceError错误
```

## RangeError

RangeError是当一个值超出有效范围时发生的错误。主要有几种情况，一是数组长度为负数，二是Number对象的方法参数超出范围，以及函数堆栈超过最大值

## TypeError

TypeError是变量或参数不是预期类型时发生的错误。比如，对字符串、布尔值、数值等原始类型的值使用new命令，就会抛出这种错误，因为new命令的参数应该是一个构造函数

```js
new 123 //TypeError: number is not a func
```

## URIError

URIError是URI相关函数的参数不正确时抛出的错误，主要涉及encodeURI()、decodeURI()、encodeURIComponent()、decodeURIComponent()、escape()和unescape()这六个函数

```js
decodeURI('%2') // URIError: URI malformed
```