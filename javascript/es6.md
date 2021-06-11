## let const

### globalThis 顶级对象

- 浏览器里面，顶层对象是 window，但 Node 和 Web Worker 没有 window

- 浏览器和 Web Worker 里面，self 也指向顶层对象，但是 Node 没有 self

- Node 里面，顶层对象是 global，但其他环境都不支持

## 变量的解构赋值

### 数组解构

按照对应位置，对变量赋值

#### 解构赋值允许默认值

```js
let [foo = true] = [];
foo; // true
let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```

#### 惰性求值

```js
function f() {
  console.log('aaa');
}
let [x = f()] = [1];
```

上面的代码不会执行函数 f 输出 aaa 因为该数组解构可以正常取到值 如果代码改为

```js
function f() {
  console.log('aaa');
}
let [x = f()] = [];
```

此时 会去执行 f 函数 进行默认赋值

### 对象解构

变量必须与属性同名 进行解构

#### 解构嵌套的对象

```js
const obj = {
  success: true,
  data: {
    username: 'chou',
    age: 18,
  },
  code: 200,
};

const {
  data: { username },
} = obj;

console.log(username); // chou
```

但是如果你要输出 data 此时就会报错 data 变量未定义

因为此时 data 作为匹配的模式 如果需要将 data 赋值 那么可以写成如下

```js
const {
  data,
  data: { username },
} = obj;
```

### 函数参数的解构赋值

```js
function add([x, y]) {
  return x + y;
}
add([1, 2]); // 3
```

个人建议使用对象解构的传参方式 例如

```js
function add({ x, y }) {
  return x + y;
}
add({ x, y });
```

这样就可以规避掉传参时 需要按顺序传参的麻烦 但是使用 TS 的话 就另说啦

### 用途

#### 交换变量的值

```js
let x = 1;
let y = 2;
[x, y] = [y, x];
```

#### 遍历 Map 结构

```js
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');
for (let [key, value] of map) {
  console.log(key + ' is ' + value);
}
// first is hello
// second is world

// 获取键名
for (let [key] of map) {
  // ...
}
// 获取键值
for (let [, value] of map) {
  // ...
}
```

## 字符串的扩展

ES6 为字符串添加了遍历器接口 使得字符串可以被 for...of 循环遍历

如果需要引用模板字符串本身，在需要时执行，可以写成函数。

```js
let func = (name) => `Hello ${name}!`;
func('Jack'); // "Hello Jack!"
```

上面代码中，模板字符串写成了一个函数的返回值。执行这个函数，就相当于执行这个模板字符串了。 这看上去很酷~~~

### 标签模板

标签模板是一种特殊的函数调用

```js
let a = 5;
let b = 10;

function tag() {
  console.log(arguments);
}

tag`${a - b} Hello ${a + b} world ${a * b}`;

// output
{
  '0': [ '', ' Hello ', ' world ', '' ],
  '1': -5,
  '2': 15,
  '3': 50
}
```

### 实例方法

#### padStart()，padEnd()

```js
'x'.padStart(5, 'ab'); // 'ababx'
'x'.padStart(4, 'ab'); // 'abax'
'x'.padEnd(5, 'ab'); // 'xabab'
'x'.padEnd(4, 'ab'); // 'xaba'
```

例如 可以用于时间格式化 比如 时分秒如果需要两位 不足两位补 0

## 数值的扩展

### Math

#### Math.trunc()

Math.trunc 方法用于去除一个数的小数部分，返回整数部分

### BigInt

- BigInt 只用来表示整数，没有位数的限制，任何位数的整数都可以精确表示

- 为了与 Number 类型区别，BigInt 类型的数据必须添加后缀 n

- BigInt 与普通整数是两种值，它们之间并不相等

- BigInt 可以使用负号（-），但是不能使用正号（+）

- 数学运算方面，BigInt 类型的+、-、\*和\*\*这四个二元运算符，与 Number 类型的行为一致。除法运算/会舍去小数部分，返回一个整数

- BigInt 对应的布尔值，与 Number 类型一致，即 0n 会转为 false，其他值转为 true

## 函数

### 函数的属性

#### length

函数的 length 属性将返回函数的参数个数 但是这个属性也不总是准确的 在函数参数指定默认值后 这个参数就不会被 length 考虑其中

#### name

函数的 name 属性，返回该函数的函数名

### 箭头函数

如果箭头函数直接返回一个对象，必须在对象外面加上括号

```js
let getTempItem = (id) => ({ id: id, name: 'Temp' });
```

（1）函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象。

（2）不可以当作构造函数，也就是说，不可以使用 new 命令，否则会抛出一个错误。

（3）不可以使用 arguments 对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

（4）不可以使用 yield 命令，因此箭头函数不能用作 Generator 函数。

## 数组的扩展

### ES6 明确将空位转为 undefined

### Array.from

还可以接受第二个参数，作用类似于数组的 map 方法，用来对每个元素进行处理，将处理后的值放入返回的数组

```js
Array.from({ length: 10 }, (item, idx) => idx + 1); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### 改变数组自身的方法

<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/data-type/array.png" />

## 对象的扩展

### 属性名表达式

ES6 后 支持 属性名是一个表达式

```js
const obj = {
  [new Date()]: 'welcome',
};
```

### Object.assign

Object.assign 同名属性会被后者覆盖而不是合并

```js
Object.assign([1, 2, 3], [4, 5]); // [4, 5, 3]
```

可以简化操作

```js
Object.assign(SomeClass.prototype, {
someMethod(arg1, arg2) {
···
},
anotherMethod() {
···
}
});
// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
···
};
SomeClass.prototype.anotherMethod = function () {
···
};
```

### Object.fromEntries

Object.fromEntries()方法是 Object.entries()的逆操作，用于将一个键值对数组转为对象。
该方法的主要目的，是将键值对的数据结构还原为对象，因此特别适合将 Map 结构转为对象。

## Symbol

Symbol 无法被隐式的转换成字符串 如下代码会报错

```js
const symbol = Symbol('hello');
symbol + 'world; // Uncaught TypeError: Cannot convert a Symbol value to a string
```

但是可以显示的转换为 string 和 布尔值 无法转换成数字

```js
symbol.toString(); // "Symbol(hello)"
!!symbol; // true
```

### Symbol.prototype.description

返回描述

```js
const sym = Symbol('foo');
sym.description; // "foo"
```

### Symbol.for / Symbol.keyFor

非常适合单例模式

```js
// 返回一个已登记的Symbol 如果没有登记就注册一个
let s1 = Symbol.for('foo');
let s2 = Symbol.for('foo');
s1 === s2; // true

// 返回一个已登记 Symbol 的 key
let s1 = Symbol.for('foo');
Symbol.keyFor(s1); // "foo"
let s2 = Symbol('foo');
Symbol.keyFor(s2); // undefined
```

### 消除魔法字符串

## Set / Map

ES6 提供了新的数据结构 Set / Map 成员的值都是唯一的

内部使用精确相等 NaN 会等于自身 +0 和-0 都会变成 0

### API

- size -> 长度

- add 添加 -> Set / Map 链式调用

- delete 删除 -> boolean

- clear 清空 -> void

- has -> boolean

### WeakSet / WeakMap

- 成员 / 键名必须是对象

- 这些对象都是弱引用 如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内

## Proxy

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写

Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”

```js
const handler = {
  set(obj, prop, value) {
    obj[prop] = value;
    console.log(`${prop}属性的set方法执行了`);
  },
  get(obj, prop) {
    console.log(`${prop}属性的get方法执行了`);
    return obj[prop];
  },
  has(obj, key) {
    console.log('has方法执行了');
    if (key in obj) {
      return true;
    } else {
      return false;
    }
  },
};

var obj = new Proxy({}, handler);
obj.name = 'chou';
obj.age = 18;
console.log(obj.name);
console.log('name' in obj);
// output
// name属性的set方法执行了
// age属性的set方法执行了
// name属性的get方法执行了
// chou
// has方法执行了
// true
```

## Reflect

### Reflect.ownKeys

Reflect.ownKeys 方法用于返回对象的所有属性，基本等同于 Object.getOwnPropertyNames 与 Object.getOwnPropertySymbols 之和

## Promise

### Promise.allSettled

在所有异步操作都执行完毕后 执行的回调

有时候，我们不关心异步操作的结果，只关心这些操作有没有结束。

这时，Promise.allSettled()方法就很有用。如果没有这个方法，想要确保所有操作都结束，就很麻烦。Promise.all()方法无法做到这一点。

## Generator

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
var hw = helloWorldGenerator();

console.log(hw.next()); // { value: 'hello', done: false }
console.log(hw.next()); // { value: 'world', done: false }
console.log(hw.next()); // { value: 'ending', done: true }
console.log(hw.next()); // { value: undefined, done: true }
```

### Generator 与状态机 (真的非常优雅)

传统的 Tick Tock 状态机 （会受到外部 ticking 状态的影响）

```js
var ticking = true;
var clock = function () {
  if (ticking) console.log('Tick!');
  else console.log('Tock!');
  ticking = !ticking;
};
```

使用 generator 函数改造

```js
var clock = function* () {
  while (true) {
    console.log('Tick!');
    yield;
    console.log('Tock!');
    yield;
  }
};
```
