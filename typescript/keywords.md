本文记录 TS 一些工具类型的实现 源码约在 `typescript/lib/lib.es5.d.ts` 1468 行处开始

在阅读这些工具类型的实现时 需要先了解一些 TS 中的关键字

## 关键字

### keyof

keyof 与 Object.keys 相似 不过 keyof 是用来获取对象类型的键的

举个 🌰

```ts
interface Person {
  age: number;
  name: string;
}

type Player = {
  age: number;
  name: string;
};

type PersonKeys = keyof Person; //  --> "age" | "name"
type PlayerKey = keyof Player; //  --> "age" | "name"
```

TODO

### typeof

typeof 用来返回一个值的 type

举个 🌰

```ts
const s = 'hello';
const n: typeof s; // --> const n: string
```

例如 当我们想把多个工具合成一个的时候 就可以用`typeof`帮我们减少重复定义

```ts
import logger from './logger';
import utils from './utils';

interface Context extends KoaContext {
  logger: typeof logger;
  utils: typeof utils;
}
```

### extends

extends 用来继承

注意 只有 interface 和 class 才可以继承

type 关键字声明的类型别名无法继承

举个 🌰

```ts
interface Person {
  name: string;
  age: number;
}

interface Player extends Person {
  item: 'ball' | 'swing';
}

const p1: Player = {
  name: 'nanshu',
  age: 18,
  item: 'ball',
};
```

### in

in 关键字可以帮助我们生成映射类型

举个 🌰

```ts
enum Letter {
  A = 'a',
  B = 'b',
  C = 'c',
}

type LetterMap = {
  [key in Letter]: string;
};

// 等价于
type _LetterMap = {
  a: string;
  b: string;
  c: string;
};

type Keys = 'name' | 'sex';

type PersonMap = {
  [key in Keys]: string;
};

// 等价于
type _PersonMap = {
  name: string;
  sex: string;
};
```

### is

is 用作类型保护

举个 🌰

```ts
function isString1(test: any): test is string {
  return typeof test === 'string';
}

function isString2(test: any): boolean {
  return typeof test === 'string';
}

const a = isString1('string'); // --> true
const b = isString2('string'); // --> true
```

这样来看 似乎两者没有差别 都能正确判断 string 类型

但是如果场景复杂一点 如下

```ts
function doSomething(params: any) {
  if (isString1(params)) {
    params.toLowerCase();
    // params.xxx(); // --> Property 'xxx' does not exist on type 'string'
  }
  if (isString2(params)) {
    params.xxx();
  }
}

doSomething('string'); // TypeError: params.xxx is not a function
```

isString1 判断后的结果会返回一个 string 的保护类型

TODO

而 isString2 因为 params 是 any 会绕过 ts 的检查

所以就算调用了一个 string 类型上不存在的属性 也不会在编码阶段有任何问题 只有在运行时候才会报错

### infer

infer 可以帮助我们推断出函数的返回值

举个例子

```ts
type a<T> = T extends (...args: any) => infer R ? R : any;

type b = a<() => string>; // type b = string
```

好了 了解这些关键字后 就可以去看 TS 内置的工具类型的实现了

## 工具类型

### Partial

让 T 中所有属性都变成可选

先用 keyof 遍历 T 中所有属性

然后 用 in 生成映射类型

```ts
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

### Required

`-?` 表示移除所有可选的属性

```ts
type Required<T> = {
  [P in keyof T]-?: T[P];
};
```

### Readonly

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};
```

### Pick

从 T 中筛选

```ts
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

举个 🌰

```ts
interface A {
  name: string;
}

interface B {
  name: string;
  age: number;
}

type C = Pick<B, keyof A>;

// 等价于
type C = {
  name: string;
};
```

### Record

用 union 类型 K 构建一个对象类型

```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
};
```

举个 🌰

```ts
type A = 'name' | 'sex';

type B = Record<A, string>;

// 等价于
type B = {
  name: string;
  sex: string;
};
```

如果 union type 中是类型 不是值 则会生成对应类型的索引签名 但只能是 string ｜ number ｜ symbol

举个 🌰

```ts
type A = string | number | symbol;

type B = Record<A, string>;

// 等价于
type B = {
  [x: string]: string;
  [x: number]: string;
  [x: symbol]: string;
};
```

### Exclude

```ts
type Exclude<T, U> = T extends U ? never : T;
```

### Extract

```ts
type Extract<T, U> = T extends U ? T : never;
```

### Omit

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

### NonNullable

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

### Parameters

```ts
type Parameters<T extends (...args: any) => any> = T extends (
  ...args: infer P
) => any
  ? P
  : never;
```

### ConstructorParameters

```ts
type ConstructorParameters<T extends abstract new (...args: any) => any> =
  T extends abstract new (...args: infer P) => any ? P : never;
```

### ReturnType

```ts
type ReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : any;
```

### InstanceType

```ts
type InstanceType<T extends abstract new (...args: any) => any> =
  T extends abstract new (...args: any) => infer R ? R : any;
```

此外还有一些限制 string 形式的工具类型

- Uppercase 约束 小写

- Lowercase 约束 大写

- Capitalize 约束 首字母大写

- Uncapitalize 约束 首字母小写

举个 🌰

```ts
const _uppercase: Uppercase<'hello'> = 'HELLO';
const _lowercase: Lowercase<'hello'> = 'hello';
const _capitalize: Capitalize<'hello'> = 'Hello';
const _uncapitalize: Uncapitalize<'Hello'> = 'hello';
```
