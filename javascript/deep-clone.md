---
title: JavaScript 深浅拷贝
date: 2020-10-23
tags:
  - JavaScript
categories:
  - JS篇
---

## concat / slice

使用 concat / slice 可以实现对一个数组的浅拷贝

但是第一层为深拷贝 如何理解呢

我们创建两个数组

- arr1 只有一层的数组

- arr2 有两层的数组

然后我们用 concat() 或 slice() 分别拷贝两份数组

然后我们来修改拷贝后的数组

通过原数组是否会被影响 来验证是否为深拷贝

<!--more-->

```js
var arr1 = [1, 2, 4, 5, 6, 7, 8, 9];
var arr2 = [1, 2, 4, [5, 6, 7, 8, 9]];

var copyArr1 = arr1.concat();
var copyArr2 = arr2.concat();

copyArr1.push(10);
copyArr2[3].push(10);

console.log(arr1); // [1, 2, 4, 5, 6, 7, 8, 9]
console.log(arr2); // [ 1, 2, 4, [ 5, 6, 7, 8, 9, 10 ] ]
```

通过最终的输出 显然易见 这两个方法只能对第一层进行深拷贝

## Object.assign(target, source......)

```js
var obj = {
  name: 'chou',
  age: 18,
  hobby: {
    color: 'blue',
    game: 'basketball',
  },
};

var copyObj = Object.assign({}, obj);

copyObj.name = 'LuckyChou';

delete copyObj.hobby.color;

console.log(obj); // { name: 'chou', age: 18, hobby: { game: 'basketball' } }
```

同样的 通过结果 我们不难发现 Object.assign() 也只是实现了对象第一层的深拷贝

也就是对属性的深拷贝 如果属性值是引用类型的话 那么还是拷贝了它的地址

## 扩展运算符...

扩展运算符也能达到类似上述的效果 但是都是浅拷贝

```js
const obj = {
  name: 'chou',
  age: 18
}

const arr = [1,2,3,4,5]

{...obj}
[...arr]
```

## JSON.parse(JSON.stringify(obj))

```js
var obj = {
  name: 'chou',
  age: 18,
  hobby: {
    color: 'blue',
    game: 'basketball',
  },
};

var copyObj = JSON.parse(JSON.stringify(obj));

copyObj.name = 'LuckyChou';

delete copyObj.hobby.color;

console.log(obj); // { name: 'chou', age: 18, hobby: { color: 'blue', game: 'basketball' } }
```

Wow! 通过结果 我们发现该方法居然实现了深拷贝 emmmmm 那么它有缺点吗

通过查阅<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify">MDN</a> 我们可以找到这样一句话

> undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略

也就是说对于 undefined 函数 symbol 用这种方法将无能为力

## DIY

既然没有完美的方案可以实现深拷贝 那么我们自己动手实现一个吧

```js
function deepClone(obj) {
  function isObject(o) {
    return (typeof o === 'object' || typeof o === 'function') && o !== null;
  }

  if (!isObject(obj)) {
    throw new Error('非对象');
  }

  let isArray = Array.isArray(obj);
  let newObj = isArray ? [...obj] : { ...obj };
  Reflect.ownKeys(newObj).forEach((key) => {
    newObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key];
  });

  return newObj;
}
```

由于深拷贝需要考虑到多种场景 实现起来也很困难 如果用于生产环境 推荐使用 lodash 的\_clone 函数
