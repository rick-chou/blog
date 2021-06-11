## 数据类型

- 基础类型存储在栈内存，被引用或拷贝时，会创建一个完全相等的变量

- 引用类型存储在堆内存，存储的是地址，多个引用指向同一个地址，这里会涉及一个“共享”的概念

### 基本数据类型

- 数值（number）

- 字符串（string）

- 布尔值（boolean）

- undefined

- null

- symbol

- bigInt

### 引用数据类型

- 对象（Object）

  - 正则（RegExp）

  - 数组（Array）

  - 函数（Function）

  - 日期（Date）

  - 数学工具库（Math）

首先明确一个点 原始类型中存储的都是对应的数值 是没有方法可以调用的 比如 undefined.toString()

但是你顺着这个思路 如果去调用 ‘1’.toString() 你会发现调用成功了

因为这种情况下 ‘1’已经被强转成了 String 类型 所以可以调用 toString 方法

## number

- js 内部，所有数字都是以 64 位浮点数形式储存，所以 1 === 1.0 为 true 即 1 和 1.0 是同一个数

### 常用方法

- parseInt

- parseFloat

- isNaN

- isFinite

- isInteger

### 常用属性

- EPSILON

```JS
const a = 0.4;
const b = 0.1;
const c = 0.3;

console.log(a - b === c); // false
console.log(Math.abs(a - b - c) < Number.EPSILON); // true
```

### 特殊数值

- NaN （not a number）

- +0 / -0

- Infinite

本质上还是 number

## String

- 无法改变字符串之中的单个字符

```js
const username = chou;
username[0] = 'z';

//username还是chou
```

### 常用方法

- charAt

  - 返回指定索引的字符

  - 如果指定的 index 值超出了该范围，则返回一个空字符串

- slice

- split

- trim / trimStart / trimEnd

  - 删除字符串**两侧**的空白 多用于 input 去除用户输入的空格

- includes

- endsWith / startsWith

- concat

- indexOf

  - 返回第一次出现的索引值 未找到返回-1

- padStart / pafEnd

  - 填充字符串达到指定长度

```js
'123'.padEnd('5', 0); //12300
'123'.padStart('5', 0); //00123
```

- repeat

```js
'1'.repeat(5); //11111
```

- toLowerCase / toUpperCase

## boolean

| 原始值           | 转换目标 | 结果                     |
| ---------------- | -------- | ------------------------ |
| number           | boolean  | 除了 -0 +0 NaN 都是 true |
| string           | boolean  | 除了 空串 都是 true      |
| undefined / null | boolean  | false                    |
| 引用类型         | boolean  | true                     |

## Object

- 对象就是一组键值对（key - value）的集合

- 对象的键会转换为 string 类型

- 对象是引用类型

具体可以参考 https://luckychou.gitee.io/FE/JS/object/

## Function

- 函数是一等公民

  - 由于函数与其他数据类型地位平等，所以在 js 中又称函数为第一公民

具体可以参考 https://luckychou.gitee.io/FE/JS/function/

## Array

- 数组是一种特殊的对象

具体可以参考 https://luckychou.gitee.io/FE/JS/array/

## undefined

- 对象没有这个属性名，属性值默认就是 undefined

- 函数定义形参不传值，默认就是 undefined

- 在数组的 find 方法中，没有找到的情况下是 undefined

- 函数定义没有返回值（return 或者 return 后面什么也不带），默认的返回值就是 undefined

- 在变量提升（预解析）阶段，只声明未定义，默认值就是 undefined

- 在 JS 的严格模式下（”use strict”），没有明确的主体，this 指的就是 undefined

## null

```js
typeof null; // object
```

## 确定数据类型

- typeof

- instanceof

- Object.prototype.toString

### typeof

- 作用于基础数据类型时 除了 null 会返回 object 其他都返回正确的数据类型

- 作用于引用数据类型时 除了 function 会返回正确的数据类型时 其他都返回 object

```js
const _undefined = undefined;
const _null = null;
const _number = 1;
const _string = 'chou';
const _boolean = true;
const _bigint = 1n;
const _symbol = Symbol();
const _regexp = new RegExp('/w/g');
const _date = new Date();
const _function = function () {};
const _array = [];

console.log(typeof _undefined);
console.log(typeof _null); // 返回object
console.log(typeof _number);
console.log(typeof _string);
console.log(typeof _boolean);
console.log(typeof _bigint);
console.log(typeof _symbol);
console.log(typeof _regexp);
console.log(typeof _date);
console.log(typeof _function); // 返回function
console.log(typeof _array);
```

### instanceof

- 通过 instanceof 我们能判断这个对象是否是之前那个构造函数生成的对象

```js
const _regexp = new RegExp('/w/g');
const _date = new Date();
const _function = function () {};
const _array = [];

console.log(_regexp instanceof RegExp); // true
console.log(_date instanceof Date); // true
console.log(_function instanceof Function); // true
console.log(_array instanceof Array); // true
```

此外 number/boolean/string 也可以用它们的构造函数来创建

### Object.prototype.toString

可见 上述两种方法都无法准确判断出每一种数据类型 那么有没有一种方法可以判断出所有的数据类型呢

答案就是`Object.prototype.toString`

例如

```js
console.log(Object.prototype.toString.call(undefined)); // [object,Undefined]
```

我们可以得到一个[object,xxx]的字符串 []中的后者就是我们想要的每一种数据类型

我们稍微包装一下方法

```js
function getDataType(type) {
  return Object.prototype.toString.call(type).slice(8, -1);
}
```

### 特殊类型比较

```js
var str1 = '1';
var str2 = new String('1');

console.log(str1 == str2); // true
console.log(str1 === str2); // false

console.log(null == null); // true
console.log(null === null); // true

console.log(undefined == undefined); // true
console.log(undefined === undefined); // true

console.log(NaN == NaN); // false
console.log(NaN === NaN); // false

console.log(/a/ == /a/); // false
console.log(/a/ === /a/); // false

console.log({} == {}); // false
console.log({} === {}); // false

console.log([] == []); // false
console.log([] === []); // false
```

## Symbol

Symbol 是一种原始数据类型 表示独一无二的值

```js
const symbol1 = Symbol();
const symbol2 = Symbol();

console.log(symbol1 === symbol2); // false
```

## BigInt

BigInt 可以表示任意大的整数

BigInt 和 Number 不能混合运算

## 四组运算

在进行四则运算的时候 都会转成基础数据类型再进行运算

### 加法运算符

运算中其中一方为字符串，那么就会把另一方也转换为字符串

如果一方不是字符串或者数字，那么会将它转换为数字或者字符串

```js
1 + '1'; // '11'
true + true; // 2
4 + [1, 2, 3]; // "41,2,3"
'a' + +'b'; //  "aNaN"
```

除了加法运算符 只要其中一方是数字，那么另一方就会被转为数字
