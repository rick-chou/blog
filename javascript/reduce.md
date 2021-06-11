## 前言

以前只知道 reduce 可以拿来做数组求和

但其实 reduce 的功能远不于此 所以在此做个总结

## 用法

`array.reduce(function(accumulator, currentValue, currentIndex, array), initialValue)`

- accumulator: 累加器 即函数上一次调用的返回值

  第一次的时候为 initialValue || arr[0]

- currentValue: 数组中函数正在处理的的值

  第一次的时候 initialValue || arr[1]

- currentIndex: (可选)数据中正在处理的元素索引

  如果提供了 initialValue 从 0 开始 否则从 1 开始

- array: (可选)调用 reduce 的数组

- initialValue: (可选)累加器的初始值。没有时 累加器第一次的值为 currentValue

注意: 在对没有设置初始值的空数组调用 reduce 方法时会报错

为了更直观的感受 我们来看一个例子

```js
//有初始值
[1, 2, 3, 4].reduce(function (accumulator, currentValue, currentIndex, array) {
  return accumulator + currentValue;
}, 10); // 20
```

| callback    | accumulator | currentValue      | currentIndex    | array        | return value |
| ----------- | ----------- | ----------------- | --------------- | ------------ | ------------ |
| first call  | 10(初始值)  | 1(数组第一个元素) | 0(有初始值为 0) | [1, 2, 3, 4] | 11           |
| second call | 11          | 2                 | 1               | [1, 2, 3, 4] | 13           |
| third call  | 13          | 3                 | 2               | [1, 2, 3, 4] | 16           |
| fourth call | 16          | 4                 | 3               | [1, 2, 3, 4] | 20           |

## 使用场景

### case1

可以做一个简单的数组求和

```js
[1, 2, 3, 4].reduce((a, b) => a + b); // 10
```

### case2

可以将二维数组扁平化

```js
[
  [1, 2],
  [3, 4],
  [5, 6],
].reduce((a, b) => a.concat(b), []); // [1, 2, 3, 4, 5, 6]
```

### case3

可以计算数组中每个元素出现的次数

思路: 初始值设置为{} 用作我们放结果的容器 然后如果容器中有这个元素 就 value+1 没有就初始化 key

```js
[1, 2, 3, 1, 2, 3, 4].reduce((items, item) => {
  if (item in items) {
    items[item]++;
  } else {
    items[item] = 1;
  }
  return items;
}, {}); // {1: 2, 2: 2, 3: 2, 4: 1}
```

### case4

数组去重

```js
// 方法一
[1, 2, 3, 1, 2, 3, 4, 4, 5].reduce((init, current) => {
  // 第一次的时候 init为空 将第一个元素push进init
  // 然后以后每次的current都在init中找是否已经存在相同的元素
  // 没找到就继续push
  if (init.length === 0 || init.indexOf(current) === -1) {
    init.push(current);
  }
  return init;
}, []); //[1, 2, 3, 4, 5]
```

```js
// 方法二
[1, 2, 3, 1, 2, 3, 4, 4, 5].sort().reduce((init, current) => {
  // 先将数组进行排序
  // 将第一个元素push 进 init
  // 判断以后的每一个元素是否和init中最后一个元素相同
  // 如同不同就继续push
  if (init.length === 0 || init[init.length - 1] !== current) {
    init.push(current);
  }
  return init;
}, []); //[1, 2, 3, 4, 5]
```
