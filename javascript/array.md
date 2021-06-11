数组的本质是一个对象 我们使用 typeof 关键字便可验证这一点

```js
typeof []; // object
```

假设我们有以下数组 来获取它的键名试试

```javascript
var arr = ['a', 'b', 'c', 'd'];
console.log(Object.keys(arr));

//[ '0', '1', '2', '3' ]
```

获取到数组的 key 为 \[ '0', '1', '2', '3' \]

所以我们可以用 arr\[0\]来获取数组中的元素

本质上是 Object\[key\]的形式获取了 value

而 arr\[0\]会被转换成 arr\['0'\] 因为对象的键名都是 string 类型

## length 属性

我们思考一个问题 arr.length 得到的一定是数组的实际长度吗

```javascript
var arr = ['a', 'b', 'c', 'd'];
console.log(arr.length); //4
arr[100] = 'z';
console.log(arr.length); //101
```

**length 属性永远比 key 最大值 大一个**

注意 因为数组本质上是一个对象 所以我们可以进行如下赋值

```javascript
const arr = [1, 2, 3, 4, 5];
arr['a'] = '6';

console.log(arr); // [ 1, 2, 3, a: 6 ]
```

上述操作会增加一个元素 a

但是数组 length 依然为 5 因为 length = Max（key） + 1

## 伪数组

伪数组不具备任何数组的方法 如果调用会报错

常见的伪数组有

- 函数内部的 arguments
- 通过选择器获取到的 nodeList

但是伪数组有 length 属性

将伪数组转换为真正的数组有如下方法

`Array.prototype.slice.call ( arrayLike )`

`Array.from ( arrayLike )`

## 数组 API

### 静态方法

#### Array.from\(arrayLike\[, mapFn\[, thisArg\]\]\)

方法从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例

- arrayLike
  - 想要转换成数组的伪数组对象或可迭代对象。
- mapFn 可选
  - 如果指定了该参数，新数组中的每个元素会执行该回调函数。
- thisArg 可选
  - 可选参数，执行回调函数 mapFn 时 this 对象。

功能：将类数组变成真正的数组 快速生成有序数组

例如 快速生成一个 1-10 的数组

```javascript
Array.from({ length: 10 }, (val, index) => index + 1); // [1,2....,10]
```

因为伪数组也有 length 属性 所以我们也可以改写成如下

```javascript
Array.from({ length: 10 }, (val, index) => index + 1);
```

#### Array.isArray\(\)

#### Array.of\(element0\[, element1\[, ...\[, elementN\]\]\]\)

返回值: 一个新的数组实例

### 成员方法

#### arr.concat\(\)

功能: 合并数组 也可用于浅拷贝

```javascript
// 浅拷贝 只拷贝最外层
const NewArray = orgArray.concat();
```

#### arr.every\(callback\(element\[, index\[, array\]\]\)\[, thisArg\]\)

- callback

  用来测试每个元素的函数，它可以接收三个参数：

  - element

    用于测试的当前值。

  - index 可选

    用于测试的当前值的索引。

  - array 可选

    调用 every 的当前数组。

返回值: 如果回调函数的每一次返回都为 truthy 值，返回 true ，否则返回 false。

```javascript
[1, 2, 3, 4, 5]
  .every((x) => x > 0) // true
  [(1, 2, 3, 4, 5)].every((x) => x > 1); // false
```

#### arr.some\(\)

同 every

返回值: 有一个通过就返回 true

#### arr.fill\(\)

参数: arr.fill\(value\[, start\[, end\]\]\)

- value

  用来填充数组元素的值。

- start 可选

  起始索引，默认值为 0。

- end 可选

  终止索引，默认值为 this.length。

返回值: 修改后的数组

```javascript
// 可用于初始化数组
Array(3).fill(0); // [0, 0, 0]
```

#### arr.filter\(callback\(element\[, index\[, array\]\]\)\[, thisArg\]\)

返回值: 一个新的、由通过测试的元素组成的数组，如果没有任何数组元素通过测试，则返回空数组。

```javascript
[1, 2, 3, 4, 5]
  .filter((x) => x > 1) // [2, 3, 4, 5]
  [(1, 2, 3, 4, 5)].filter((x) => x > 6); // []
```

#### arr.find\(callback\[, thisArg\]\)

返回值: 数组中**第一个**满足所提供测试函数的元素的值，否则返回 undefined。

```javascript
[1, 2, 3, 4, 5]
  .find((x) => x > 6) //undefined
  [(1, 2, 3, 4, 5)].find((x) => x > 2); //3
```

#### arr.findIndex\(\)

同 find 区别在于返回值

数组中通过提供测试函数的**第一个**元素的索引。否则，返回-1。

#### arr.flat\(\[depth\]\)

- depth: 深度 默认为 1

将数组扁平化

返回值: 一个新的数组实例。

#### arr.flatMap\(\)

flatMap\(\) = map\(\) + flat\(1\)

#### arr.forEach\(callback\(currentValue \[, index \[, array\]\]\)\[, thisArg\]\)

返回值: 没有返回值 undefined

#### arr.map\(\)

效果同 forEach\(\) 区别在于 map\(\)有返回值

返回值: 原数组每个元素执行回调函数的结果组成的新数组

#### arr.includes\(\)

返回值: boolean

```javascript
[1, 2, 3].includes(2); // true
[1, 2, 3].includes(4); // false
```

#### arr.indexOf\(searchElement\[, fromIndex\]\)

- fromIndex\(可选\)
  - 指定开始查找的位置

返回值: 查找元素的索引下标 若找不到则为-1

```javascript
var array = [2, 5, 9];
array.indexOf(2); // 0
array.indexOf(7); // -1
array.indexOf(9, 2); // 2
array.indexOf(2, -1); // -1
array.indexOf(2, -3); // 0
```

#### arr.join\(\)

```javascript
var a = ['Wind', 'Rain', 'Fire'];
var myVar1 = a.join(); // myVar1的值变为"Wind,Rain,Fire"
var myVar2 = a.join(', '); // myVar2的值变为"Wind, Rain, Fire"
var myVar3 = a.join(' + '); // myVar3的值变为"Wind + Rain + Fire"
var myVar4 = a.join(''); // myVar4的值变为"WindRainFire"
```

#### arr.reduce\(callback\(accumulator, currentValue\[, index\[, array\]\]\)\[, initialValue\]\)

- accumulator: 累加器 即函数上一次调用的返回值。

  第一次的时候为 initialValue \|\| arr\[0\]

- currentValue: 数组中函数正在处理的的值。

  第一次的时候 initialValue \|\| arr\[1\]

- currentIndex: \(可选\)数据中正在处理的元素索引

  如果提供了 initialValue 从 0 开始 否则从 1 开始

- array: \(可选\)调用 reduce 的数组
- initialValue: \(可选\)累加器的初始值。没有时 累加器第一次的值为 currentValue

  注意: 在对没有设置初始值的空数组调用 reduce 方法时会报错。

返回值: 函数累计处理的结果

为了更直观的感受 我们来看一个例子

```javascript
//有初始值
[1, 2, 3, 4].reduce(function (accumulator, currentValue, currentIndex, array) {
  return accumulator + currentValue;
}, 10); // 20
```

| callback    | accumulator  | currentValue        | currentIndex      | array          | return value |
| :---------- | :----------- | :------------------ | :---------------- | :------------- | :----------- |
| first call  | 10\(初始值\) | 1\(数组第一个元素\) | 0\(有初始值为 0\) | \[1, 2, 3, 4\] | 11           |
| second call | 11           | 2                   | 1                 | \[1, 2, 3, 4\] | 13           |
| third call  | 13           | 3                   | 2                 | \[1, 2, 3, 4\] | 16           |
| fourth call | 16           | 4                   | 3                 | \[1, 2, 3, 4\] | 20           |

#### arr.reverse\(\)

返回值: 颠倒后的数组\(会直接修改原数组\)

#### arr.slice\(\[begin\[, end\]\]\)

若省略 则默认从 0 开始

返回值: 一个新的数组实例\(不会修改原数组\)

功能: 切割数组/浅拷贝/转化为真正的数组

```javascript
Array.prototype.slice.call(arguments);
```

#### arr.sort\(\[compareFunction\]\)

```javascript
var numbers = [4, 2, 5, 1, 3];
numbers.sort((a, b) => a - b); // 升序
numbers.sort((a, b) => b - a); // 降序
```

返回值: 排序后的数组\(修改原数组\)

#### array.splice\(start\[, deleteCount\[, item1\[, item2\[, ...\]\]\]\]\)

- deleteCount\(可选\)

  整数，表示要移除的数组元素的个数。

  - `>0 删除元素`
  - `<0 添加元素`

#### 增加/删除

- pop\(\) 删除最后一个元素 返回值为被删除元素
- push\(\) 在数组最后添加一个元素 返回值为新数组的长度
- shift\(\) 删除第一个元素 返回值为被删除的元素
- unshift\(\) 在数组开头添加一个元素 返回值为新数组的长度

## 总结

![](https://gitee.com/LuckyChou/blog-images/raw/master/js/data-type/array.png)

## 数组排序

```javascript
// 升序
arr.sort((x, y) => x - y);
// 降序
arr.sort((x, y) => y - x);
```

## 数组反转

```javascript
// 实现一
arr.reverse();

// 实现二
arr.reduce((prev, curr) => {
  prev.unshift(curr);
  return prev;
}, []);
```

## 数组去重

### 实现一

用 ES6 Set 的特性 没有重复的元素

### 实现二

先将数组排序 然后比较相邻元素 如果相同 就移到下一个元素

### 实现三

双重 for 循环

### 比较

具体代码如下

```javascript
function getArr(arr) {
  const timer = 10000;
  for (let i = 0; i < timer; i++) {
    arr[i] = Math.floor(Math.random() * 100);
  }
  return arr;
}

let arr1 = [];
let arr2 = [];
let arr3 = [];

// 生成了 3个 数组长度为timer的数组
arr1 = getArr(arr1);
arr2 = getArr(arr2);
arr3 = getArr(arr3);

// case 1
console.time('case1');
arr = [...new Set(arr1)];
console.timeEnd('case1');

// case 2
console.time('case2');
arr2 = arr2.sort((x, y) => x - y);
const res2 = [];
for (let i = 0; i < arr2.length; i++) {
  if (arr2[i] !== arr2[i + 1]) {
    res2.push(arr2[i]);
  }
}
console.timeEnd('case2');

// case3
console.time('case3');
const res3 = [];
for (let i = 0; i < arr3.length; i++) {
  for (let j = i + 1; j < arr3.length; j++) {
    if (arr3[i] == arr3[j]) {
      break;
    }
    if (j == arr3.length - 1) {
      res3.push(arr3[i]);
    }
  }
}
console.timeEnd('case3');

// output
// case1: 0.467ms
// case2: 5.254ms
// case3: 11.034ms
```

可见 利用 ES6 的 Set 是效率最高的一种方法

## 数组乱序

### 实现

取两个数组长度范围内的下标 交换位置

```javascript
var arr = [1, 2, 3, 4, 5, 6, 7, 8];

function random(arr) {
  const timer = 1000;
  var x, y, temp;
  for (let i = 0; i < timer; i++) {
    x = Math.floor(Math.random() * arr.length);
    y = Math.floor(Math.random() * arr.length);
    temp = arr[x];
    arr[x] = arr[y];
    arr[y] = temp;
    // ES6 写法
    // ;[arr[x], arr[y]] = [arr[y], arr[x]]
  }
  return arr;
}
```

## 数组扁平化

### 实现一

调用 `Array.prototype.flat`

### 实现二

如果都是数字 可以用 toString 方法 转化为字符串 再用 split 切割

### 实现三

递归调用

```javascript
var arr = [1, [1, 2, 3], 4, 5];
var res = [];
function flat(arr) {
  // case 1
  // return arr.flat()
  // case 2
  // return arr
  //   .toString()
  //   .split(',')
  //   .map((item) => +item)
  // case 3
  // for (var i = 0; i < arr.length; i++) {
  //   if (arr[i] instanceof Array) {
  //     flat(arr[i])
  //   } else {
  //     res.push(arr[i])
  //   }
  // }
  // return res
}
```
