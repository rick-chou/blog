故事的开始从一个色盘的 HSV 进制转换开始

当我在控制台输入 👇👇👇👇👇👇

```js
parseInt(011, 2); // --> NaN;
parseInt(11, 2); // --> 3
```

麻了 我麻了

## parseInt

以下为 W3C 上的定义

parseInt() 函数可解析一个字符串，并返回一个整数。

当参数 radix 的值为 0，或没有设置该参数时，parseInt() 会根据 string 来判断数字的基数。

当忽略参数 radix , JavaScript 默认数字的基数如下:

- 如果 string 以 "0x" 开头，parseInt() 会把 string 的其余部分解析为十六进制的整数。

- 如果 string 以 0 开头，那么 ECMAScript v3 允许 parseInt() 的一个实现把其后的字符解析为八进制或十六进制的数字。

- 如果 string 以 1 ~ 9 的数字开头，parseInt() 将把它解析为十进制的整数。

再来看 `parseInt(011, 2)`

011 会被 parseInt 理解为八进制 从而转换为 9

而二进制只有 0 和 1 所以结果为 NaN

### 思考题 🤔

```js
['1', '2', '3'].map(parseInt);
```

答案在 👇👇👇👇👇

```text
该语句在执行时候 等价于`['1','2','3'].map((value,index) => parseInt(value,index))`
parseInt('1',0) // 基数为0时 当10进制来处理 就是自身 --> 1
parseInt('2',1) // 基数为1时 只有一个状态0 而2不是一个一进制的数 --> NaN
parseInt('3',2) // 基数为2时 状态有0/1/2  而3不是一个二进制的数 --> NaN
```

### more

```js
parseInt(0.1) --> ?
parseInt(0.01) --> ?
parseInt(0.001) --> ?
parseInt(0.0001) --> ?
parseInt(0.00001) --> ?
parseInt(0.000001) --> ?
parseInt(0.0000001) --> ?
```

如果是这样呢

```js
parseInt(1e1) --> ?
parseInt(1e2) --> ?
parseInt(1e3) --> ?
parseInt(1e4) --> ?
......
```

更多涉及 parseInt 可参考 <a href="https://2ality.com/2013/01/parseint.html">国外小哥哥的博客</a>

## 进制间的转换

### 转十进制

```js
/**
 * parseInt
 * @param string  要转换成十进制的数
 * @param radix 要转换成十进制的数的进制 【 2 - 36 】 默认为 10
 */
parseInt(string, radix);
```

### 通用方法

```js
/**
 * toString
 * @param radix 要转换的进制 【 2 - 36 】 默认为 10
 */
Number.prototype.toString(radix);
```
