## 背景

最近 开始接触到一些 ES6 以后的新特性 使用起来确实方便了不少

尤其是最近有一个项目 在方法中需要传入 index 但是在做非空验证的时候 如果传入的 index 是 0 也会被判断为 false 这就十分头疼了

冗余的判断不仅使代码的可读性变差 而且会使代码变得不简洁

但是在接触到可选链操作符后 emmmmm 这也太香了 避免了 && 和 多余的非空校验

所以决定以此为开始 陆续的写一些 ES6 ES7...之后的新特性

## 可选链操作符

### 初识

这里我引用一段 MDN 上的解释

> 可选链操作符( ?. )允许读取位于连接对象链深处的属性的值，而不必明确验证链中的每个引用是否有效。?. 操作符的功能类似于 . 链式操作符，不同之处在于，在引用为空(nullish ) (null 或者 undefined) 的情况下不会引起错误，该表达式短路返回值是 undefined。与函数调用一起使用时，如果给定的函数不存在，则返回 undefined。

或许 你还是?????

不要急 我们来看一道例题

```js
const adventurer = {
  name: '林克',
  weapon: {
    name: '大师剑',
  },
};

const weaponName = adventurer.weapon?.name;
console.log(weaponName);
// expected output: 大师剑

console.log(adventurer.savePrincess?.());
// expected output: undefined

const shieldName = adventurer.shield?.name;
console.log(shieldName);
// expected output: undefined
```

上述例子`adventurer.weapon?.name`

我们试图去寻找 adventurer 对象下的 weapon 属性下的 name 属性

adventurer 对象有 weapon 属性 所以返回我们可以取出我们的大师剑

然后是`adventurer.savePrincess?.()`

显然 adventurer 下没有 savePrincess 这个方法 所以救公主？？？ 不存在的 直接返回 undefined

最后是`adventurer.shield?.name`

同样的 我们发现 adventurer 对象下也没有 shield 属性 所以直接返回 undefined

好啦 至此 我们大概理解了可选链是怎么一回事

接下来 我们来看看 可选链有什么需要注意的地方

### 注意点

#### 可选链不能用于赋值

```js
let object = {};
object?.property = 1; // Uncaught SyntaxError: Invalid left-hand side in assignment
```

#### 短路计算

当在表达式中使用可选链时，如果左操作数是 null 或 undefined，表达式将不会被计算，例如：

```js
let potentiallyNullObj = null;
let x = 0;
let prop = potentiallyNullObj?.[x++];

console.log(x); // x 将不会被递增，依旧输出 0
```

最后再举一个自己在实际开发中的例子

假如有一个方法`setStudentResult(action)` 它接收一个对象 action

为了让我们的代码健壮 我们必须在方法内部做出参数的非空校验 显然下面这样是不行的

```js
const list = {};

/**
 * @param {*} action
 */
function setStudent(action) {
  list[action.index] = action.student;
}
```

如果我们直接调用`setStudent()` 显然整个程序都会崩溃

所以在以前 我们可以将方法体改成这样

```js
// 同时包含函数接收了action参数 并且参数中有我们需要的内容
function setStudent(action) {
  if (action) {
    if (action.index && action.student) {
      list[action.index] = action.student;
    }
  }
}
```

但是 如果使用可选链操作符 我们就可以将方法定义成如下

```js
function setStudent(action) {
  if (action?.index && action?.student) {
    list[action.index] = action.student;
  }
}
```

可以看到 无论是可读性 还是简洁程度 可选链操作符都更胜一筹

## 空值合并操作符

还是引用一段 MDN 上解释

> 空值合并操作符（??）是一个逻辑操作符，当左侧的操作数为 null 或者 undefined 时，返回其右侧操作数，否则返回左侧操作数。

### 对比 ||

我们知道 || 逻辑或运算符 也具有相似的功能

也就是在 || 左侧操作数为假值时 会返回右侧 操作数

JS 中的假值有

- 0

- ''

- NaN

- null

- undefined

所以设想一个场景 我们对一个 function 中的参数使用 || 运算符 来判断用户是否输入

```js
function addNum(a, b) {
  var a = a || 0;
  var b = b || 0;
  return a + b;
}
```

上述代码我们用 || 运算符来判断用户 用户输入的合法性

但是这样我们就无法排除 0 这种情况

如果用户就是输入了 0 但是 0 依旧是一个假值 所以还是会返回 || 操作符右侧是操作数

这就与我们的预期不一致了

而 ?? 空值合并操作符就只是检验了 null 和 undefined

### 注意点

#### 短路计算

与 OR 和 AND 逻辑操作符相似，当左表达式不为 null 或 undefined 时，不会对右表达式进行求值

```js
var a = 1;
undefined ?? a++;
console.log(a); // 1

var b = 1;
true ?? b++;
console.log(b); // 2
```

#### 不能与 AND 或 OR 操作符共用

```js
null || undefined ?? "foo"; // 抛出 SyntaxError
true || undefined ?? "foo"; // 抛出 SyntaxError
```

#### 与可选链操作符连用

两个操作符都是针对 undefined 和 null 两个值

所以我们可以结合这两个操作符

```js
let customer = {
  name: 'chou',
  details: { age: 100 },
};
let customerCity = customer?.city ?? '荒野之息';
console.log(customerCity); // 荒野之息
```
