## 选择题

### case1

以下这段代码的输出为

```js
var res = [
  [0, 1],
  [2, 3],
].reduce(
  (acc, cur) => {
    return acc.concat(cur);
  },
  [1, 2]
);

console.log(res);
```

```text
答案: [ 1, 2, 0, 1, 2, 3 ]
解析: [1,2]为初始值 一开始的acc就是[1,2]
```

### case2

以下这段代码的输出为

```js
function getAge(age) {
  if (age > 18) {
    const message = 'U are old enough';
  } else {
    const message = 'U are too young';
  }
  return message;
}

getAge(21);
```

{% folding yellow, 查看答案 %}

```text
答案: ReferenceError
解析: const有块级作用域 在return语句中 在当前作用域下找不到message变量
```

{% endfolding %}

### case3

以下这段代码的输出为

```js
(() => {
  let x = (y = 10);
})();

console.log(typeof x);
console.log(typeof y);
```

{% folding yellow, 查看答案 %}

```text
答案:  undefined number
解析:  let x = (y = 10) 相当于 y = 10 let x = y
      而y则变成了全局变量 可以用window.y获取到
      let有块级作用域 在外部无法访问
```

{% endfolding %}

### case4

以下这段代码的输出为

```js
const obj = {
  name: 'chou',
  age: 18,
};

Object.freeze(obj);

obj.age = 20;

console.log(obj);
```

{% folding yellow, 查看答案 %}

```text
答案:  { name: 'chou', age: 18 }
解析:  Object.freeze(obj)使得我们无法对obj进行修改
```

{% endfolding %}

### case5

以下这段代码的输出为

```js
function getParams() {
  console.log(arguments);
}

let name = 'chou';

let age = 18;

getParams`${name} is ${age} years old`;
```

{% folding yellow, 查看答案 %}

```text
答案:  { '0': [ '', ' is ', ' years old' ], '1': 'chou', '2': 18 }
```

{% endfolding %}

### case6

以下这段代码的输出为

```js
const colorConfig = {
  red: true,
  yellow: false,
  blue: true,
};

const color = ['red', 'yellow', 'blue'];

console.log(colorConfig.color[1]);
```

{% folding yellow, 查看答案 %}

```text
答案:  TypeError
解析:  colorConfig.color[1]会先执行colorConfig.color 返回undefined
      然后undefined[1] 会返回Cannot read property '1' of undefined
```

{% endfolding %}

### case7

以下这段代码的输出为

```js
console.log(
  [1, 2, 3].map((item) => {
    if (typeof item === 'number') return;
    return item * 2;
  })
);
```

{% folding yellow, 查看答案 %}

```text
答案:  [ undefined, undefined, undefined ]
解析:  if (typeof item === 'number') return 语句 条件成立 但是没有返回值 默认返回undefined
      return item * 2 中 item 都已经是undefined
```

{% endfolding %}

### case8

以下这段代码的输出为

```js
['1', '2', '3'].map(parseInt);
```

{% folding yellow, 查看答案 %}

```text
答案:  [ 1, NaN, NaN ]
解析:  该语句在执行时候 等价于['1','2','3'].map((value,index) => parseInt(value,index))
parseInt('1',0) // 基数为0时 当10进制来处理 1*10^0
parseInt('2',1) // 基数为1 只有一个状态0 2不在里面 推出NaN
parseInt('3',2) // 基数为2 状态有0 1 2 推出3不在里面 NaN
```

{% endfolding %}

### case8

以下这段代码的输出为

```js
var b = 10(function b() {
  b = 20;
  console.log(b);
})();
```

{% folding yellow, 查看答案 %}

```text
答案:  [Function: b]
解析:  显然 干扰我们的只有立即执行函数内部的b = 20 这条语句到底有没有赋值成功
      答案是立即执行函数无法被赋值 因为JS内部将立即调用函数当成了const定义的常量
      我们可以在严格模式下 测试这段代码
      var b = 10
      (function b () {
        'use strict'
        b = 20
        console.log(b)
      })()
      我们可以看到 控制台马上就给我们反馈了一个TypeError: Assignment to constant variable.
```

{% endfolding %}

### case9

以下这段代码的输出为

```js
var obj = {
  2: 3,
  3: 4,
  length: 2,
  splice: Array.prototype.splice,
  push: Array.prototype.push,
};

obj.push(1);

console.log(obj);
```

{% folding yellow, 查看答案 %}

```text
答案:  {
        '2': 1,
        '3': 4,
        length: 3,
        splice: [Function: splice],
        push: [Function: push]
      }
解析:  只有一个对象有splice就会变成伪数组
      这一题的push会使length+1 然后在下标为length的地方插入新值
```

{% endfolding %}

### case10

以下这段代码的输出为

```js
const person = {
  name: 'chou',
  age: 18,
};

const changeAge = (x = { ...person }) => (x.age += 1);

const changeAgeAndName = (x = { ...person }) => {
  x.age += 1;
  x.name = 'Sam';
};

changeAge(person);
changeAgeAndName();

console.log(person);
```

{% folding yellow, 查看答案 %}

```text
答案:  { name: 'chou', age: 19 }
解析:  第一处函数调用我们传入了参数 而参数是一个对象 方法体中的操作会直接影响到这个对象
      而第二处函数调用我们没有传入参数  但是它有默认值 person对象的一个拷贝 所以不会影响到我们最终的
```

{% endfolding %}

## 手写题

### Promise 实现红绿灯交替

> 红灯三秒亮一次，绿灯一秒亮一次，黄灯 2 秒亮一次；如何让三个灯不断交替重复亮灯？（用 Promise 实现）

{% folding yellow, 查看答案 %}

```js
function red() {
  console.log('red');
}

function green() {
  console.log('green');
}

function yellow() {
  console.log('yellow');
}

function light(timer, cb) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(cb());
    }, timer);
  });
}

function step() {
  light(3000, red)
    .then(() => {
      return light(2000, green);
    })
    .then(() => {
      return light(1000, yellow);
    })
    .then(() => {
      step();
    });
}

step();
```

{% endfolding %}

### 最大公共子串

> input : ['floor', 'flower', 'flow'] ===> output: flo

{% folding yellow, 查看答案 %}

```js
function getCommon(arr) {
  let idx = 0;
  let str = arr[0].slice(0, idx);
  while (arr.every((item) => item.includes(str))) {
    if (str === arr[0]) {
      return str;
    }
    str = arr[0].slice(0, ++idx);
  }
  return str.slice(0, str.length - 1);
}
```

{% endfolding %}

### 反转字符串

> 反转字符串 多个空格最后只显示一个
> input : hello lucky chou! ===> chou! lucky hello

{% folding yellow, 查看答案 %}

```js
function reverse(str) {
  let res = '';
  const arr = str
    .trim()
    .split(' ')
    .reduce((prev, curr) => {
      if (curr.length > 0) {
        prev.push(curr);
      }
      return prev;
    }, [])
    .reverse()
    .forEach((item) => {
      res += item + ' ';
    });

  return res.trim();
}
```

{% endfolding %}

### 模板字符串

```js
temp('hello i am ${name} i like ${sport}', {
  sport: 'basketball',
  name: 'chou',
});
```


对正则不太熟悉 暂时想到了这种解法

```js
function temp(str, obj) {
  for (let key in obj) {
    if (str.includes(key)) {
      str = str.replace(key, obj[key]);
    }
  }

  str = str.replace(/\${/g, '').replace(/\}/g, '');
  return str;
}
```
