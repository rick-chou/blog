> 仓库地址 https://github.com/LuckyChou710/code-traveling/tree/main/03-js-training-camp/basic/RegExp

## 创建一个正则表达式

### 字面量形式

```js
const regExp1 = /[a-z]/i;
```

### 构造函数

```js
const regExp2 = new RegExp('[a-z]', 'i'); // 字符串模式
const regExp3 = new RegExp(/[a-z]/, 'i'); // 字面量模式
```

正则表达式也是对象 属于引用类型

所以在进行比较的时候 是引用地址的比较

```js
console.log(regExp1 === regExp2); // false
console.log(regExp2 === regExp3); // false
console.log(/[a-z]/i === /[a-z]/i); // false
```

## 正则表达式修饰符

- i 对大小写不敏感

- g 全局匹配

- m 多行匹配

- s 允许 . 匹配换行符

- u 使用 unicode 码的模式进行匹配

- y 执行“粘性(sticky)”搜索,匹配从目标字符串的当前位置开始

### 换行符

- \n unix 换行

- \r mac 回车

- \r\n windows 回车换行

- CRLF 回车换行

- LF 换行

### sticky

```js
const regY = /[a-z]/y;
console.log(str.match(regY)); // [ 'a', index: 0, input: 'aBcDeFg', groups: undefined ]
// lastIndex 可以指定索索位置
regY.lastIndex = 2;
console.log(str.match(regY)); // [ 'c', index: 2, input: 'aBcDeFg', groups: undefined ]

// 除了 y 之外 这种操作都无效
regI.lastIndex = 2;
console.log(str.match(regI)); // [ 'c', index: 2, input: 'aBcDeFg', groups: undefined ]
```

## 实例方法

```js
// test 该正则在字符串里是否有匹配
console.log(/a/.test('a'));

// exec 在该字符串中执行匹配项的搜索
console.log(/a/.exec('aaa'));
```

### lastIndex

lastIndex 是正则表达式的一个可读可写的整型属性，用来指定下一次匹配的起始索引

```js
const re = /a/g;
while (re.exec('aaa') !== null) {
  console.log(re.lastIndex);
}
```

### 一些特殊的实例方法

```js
console.log(/a/[Symbol.match]('abc')); // [ 'a', index: 0, input: 'abc', groups: undefined ]

console.log(/a/[Symbol.matchAll]('abc')); // Object [RegExp String Iterator] {}

console.log(/a/[Symbol.search]('abc')); // 0

console.log(/-/[Symbol.split]('a-b-c')); // [ 'a', 'b', 'c' ]

console.log(/[a-z]/i.toString()); // /[a-z]/i 重写了Object.prototype.toString方法
```

## 实例属性

```js
console.log(/[a-z]/i.flags); // i
console.log(/./s.dotAll); // true
console.log(/./g.global); // true
console.log(/./u.unicode); // true
console.log(/./i.ignoreCase); // true
console.log(/./m.multiline); // true
console.log(/./y.sticky); // true
console.log(/[a-z]/i.source); // [a-z]
```

## 组和范围

```js
// 匹配括号内的任意字符
const regExp1 = /[abc]/g;
// 匹配不在括号内的字符
const regExp2 = /[^abc]/g;

console.log('abcd'.match(regExp1)); // [ 'a', 'b', 'c' ]
console.log('abcd'.match(regExp2)); // [ 'd' ]

// 匹配括号内的任意字符
const regExp3 = /(a|b|c)/g;
console.log('abcd'.match(regExp3)); // [ 'a', 'b', 'c' ]

// 可以写 A-z 不可以写成 a-Z
// 因为ASCII码表上A在a前面
const regExp4 = /[A-z]/g;
console.log('abcdEFG'.match(regExp4)); // [ 'd' ]
console.log('A'.charCodeAt()); // 65
console.log('a'.charCodeAt()); // 97

// x|y 匹配x或y中的任意字符
console.log('abcd'.match(/a|b/g)); // [ 'a', 'b' ]

// (?<Name>x) 具名捕获组: 匹配"x"并将其存储在返回的匹配项的groups属性中
console.log('web-doc'.match(/-(?<customName>doc)/).groups); // { customName: 'doc' }
```

## 断言

```js
/**
 * https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions/Assertions
 * 断言的组成之一是边界。对于文本、词或模式，边界可以用来表明它们的起始或终止部分
 */

// ^匹配输入的开头
console.log('abcde'.match(/^a/g)); // [ 'a' ]
console.log('abcde'.match(/^b/g)); // null

// $匹配输入的结束
console.log('abcde'.match(/e$/g)); // [ 'e' ]
console.log('abcde'.match(/a$/g)); // null

// \b 匹配单词边界
console.log('chou'.match(/ou\b/g)); // [ 'ou' ]
console.log('chou'.match(/\bch/g)); // [ 'ch' ]

// \B
console.log('chou'.match(/\Bh/g)); // [ 'h' ]
console.log('chou'.match(/ch\B/g)); // [ 'ch' ]

// x(?=y) x 被 y 跟随时匹配 x
console.log('luckychou'.match(/lucky(?=chou)/g)); // [ 'lucky' ]
console.log('lucky chou'.match(/lucky(?=chou)/g)); // null

// x(?!y) x 没有被 y 紧随时匹配 x
console.log('luckychou'.match(/lucky(?!chou)/g)); // null
console.log('lucky chou'.match(/lucky(?!chou)/g)); // [ 'lucky' ]

// (?<=y)x x 跟随 y 的情况下匹配 x
console.log('luckychou'.match(/(?<=lucky)chou/g)); // [ 'chou' ]
console.log('lucky chou'.match(/(?<=lucky)chou/g)); // null

// (?<!y)x x 不跟随 y 时匹配 x
console.log('luckychou'.match(/(?<!lucky)chou/g)); // null
console.log('lucky chou'.match(/(?<!lucky)chou/g)); // [ 'chou' ]
```

## 量词

```js
/**
 * https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions/Quantifiers
 * https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/quantifiers-in-regular-expressions
 * 量词表示要匹配的字符或表达式的数量
 */

// x* 匹配上一个元素零次或多次  === {0,}
console.log('abbbbbc'.match(/ab*/g)); // [ 'abbbbb' ]

// x+ 匹配上一个元素一次或多次  === {1,}
console.log('abbbbbc'.match(/ab+/g)); // [ 'abbbbb' ]
console.log('ac'.match(/ab+/g)); // null

// x? 匹配上一个元素一次或多次
console.log('abbbbbc'.match(/ab?/g)); // [ 'ab' ]

// x{n} 恰好匹配 n 次
console.log('abbbbbc'.match(/ab{1}/g)); // [ 'ab' ]
console.log('abbbbbc'.match(/ab{2}/g)); // [ 'abb' ]

// x{n,} 至少匹配 n 次
console.log('abbbbbc'.match(/ab{1}/g)); // [ 'ab' ]

// x{n,m} 匹配 n 到 m 次
console.log('abbbbbc'.match(/ab{1,3}/g)); // [ 'abbb' ]
```

## 字符类

```js
/**
 * https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions/Character_Classes
 * 字符类可以区分各种字符，例如区分字母和数字
 */

/**
 * 换行 / 行结束符
 * \n unix 换行
 * \r mac 回车
 * \r\n windows 回车换行
 * CRLF 回车换行
 * LF 换行
 */

const str = `
1
a
B
~
_
`;

// . 匹配任意字符 除了换行和行结束符
console.log(str.match(/./g)); // [ '1', 'a', 'B', '~', '_' ]

// \w 查找数字/字母/下划线
console.log(str.match(/\w/g)); // [ '1', 'a', 'B', '_' ]

// \W 与\w相反
console.log(str.match(/\W/g)); // ['\n', '\n', '\n', '\n', '~', '\n', '\n']

// \d 查找数字
console.log(str.match(/\d/g)); // [ '1' ]

// \D 查找非数字
console.log(str.match(/\D/g)); // ['\n', '\n', 'a', '\n', 'B', '\n', '~', '\n', '_', '\n']

// \s 查找空白字符
console.log(str.match(/\s/g)); // [ '\n', '\n', '\n', '\n', '\n', '\n' ]

// \S 查找非空白字符
console.log(str.match(/\S/g)); // [ '1', 'a', 'B', '~', '_' ]

// \0 查找 NULL 字符

// \n 查找换行符

// \r 查找回车符

// \f 查找换页符

// \t 查找制表符

// \v 查找垂直制表符

// \xxx	查找以八进制数 xxx 规定的字符

// \xdd	查找以十六进制数 dd 规定的字符

// \uxxxx	查找以十六进制数 xxxx 规定的 Unicode 字符
// 匹配汉字
console.log('南树'.match(/[\u4e00-\u9fa5]/g));
```
