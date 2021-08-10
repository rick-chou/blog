## 写在前面

本系列是博主在学习 TS 过程中 阅读如下文档做出的笔记 如果有错误 希望在评论区指出哦 🤩🤩🤩

> TypeScript 中文手册 https://typescript.bootcss.com/

> TypeScript 入门教程 https://ts.xcatliu.com/

> 深入理解 TypeScript https://jkchao.github.io/typescript-book-chinese/

> TypeScript 官网 https://www.typescriptlang.org/docs/handbook/intro.html

> babel https://babeljs.io/

预计将会更新如下内容

- TS 小册 - 类型系统

- TS 小册 - 高级类型

- TS 小册 - 模块

- TS 小册 - 工具函数

- TS 小册 - JSX 和 React

...... (TODO) 好吧 我也不知道 最后会是怎样 😛😛😛

## 类型系统

> 本文涉及的代码 https://github.com/LuckyChou710/ReStart-FE/tree/main/base/ts/typescript%E7%B1%BB%E5%9E%8B%E7%B3%BB%E7%BB%9F

### 基本数据类型

```ts
// boolean
let isDone: boolean;

// number
let count: number;

// string
let username: string;

// symbol
let unique: symbol;

// bigint
let bigBigBigNum: bigint;

/**
 * 默认情况下null和undefined是所有类型的子类型
 * 就是说你可以把null和undefined赋值给number类型的变量
 * 指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自
 */

// undefined
let u: undefined;

// null
let n: null;

/**
 * never类型是任何类型的子类型，也可以赋值给任何类型
 * 然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）
 * 即使any也不可以赋值给never
 */

function error(message: string): never {
  throw new Error(message);
}
```

### 数组

```ts
// 数组
let arr1: number[];

// 数组泛型
let arr2: Array<number>;

// 元组
let arr3: [number, string];
```

### 函数

```ts
// 为函数定义类型
type Add = (x: number, y: number) => number;

function add1(x: number, y: number): number {
  return x + y;
}

const add2: Add = function (x: number, y: number): number {
  return x + y;
};

// 剩余参数
function add3(x: number, ...y: Array<number>) {
  return x + y.reduce((x, y) => x + y);
}
add3(1, 2, 1, 2, 3, 4);

// 默认参数
// 如果你想要默认参数生效的话 就把默认参数放在参数序列的最后一项吧
// 如下定义 就会造成歧义 编译器不知道传入一个参数的时候 到底想表达的是哪一个值
// 最终 我们如果想避免程序报错的话 就不得不乖乖的传入第二个参数 这样我们的默认参数就失效了
function add4(x: number = 2, y: number) {
  return x + y;
}
add4(3, 5);

// 可选参数
// 可选参数必须放在参数序列的最后一项
function add5(x: number, y?: number) {
  return x + y;
}

// 函数重载
function parse(x: any): any {
  if (typeof x === 'number') return x;
  if (typeof x === 'string') return x.length;
}
```

### 枚举

```ts
/**
 * 默认情况下，从0开始为元素编号
 * 也可以手动为某个枚举值赋值 下一个枚举值为前一个枚举值 + 1
 */

enum Color {
  Red,
  Green,
  Blue,
}

Color.Blue; // -->2

enum Count {
  one = 1,
  two,
  three,
}

Count.three; // --> 3

// 字符串枚举
enum Lang {
  js = 'javascript',
  ts = 'typescript',
  jsx = 'react',
  py = 'python',
}
```

如果你感兴趣它是如何实现的话 你可以在 babel 官网去转换它们

大致它们被 babel 转换成如下

```js
'use strict';

var Color;

(function (Color) {
  Color[(Color['Red'] = 0)] = 'Red';
  Color[(Color['Green'] = 1)] = 'Green';
  Color[(Color['Blue'] = 2)] = 'Blue';
})(Color || (Color = {}));

var Count;

(function (Count) {
  Count[(Count['one'] = 1)] = 'one';
  Count[(Count['two'] = 2)] = 'two';
  Count[(Count['three'] = 3)] = 'three';
})(Count || (Count = {}));

var Lang;

(function (Lang) {
  Lang['js'] = 'javascript';
  Lang['ts'] = 'typescript';
  Lang['jsx'] = 'react';
  Lang['py'] = 'python';
})(Lang || (Lang = {}));
```

### 类

```ts
/**
 * 类继承接口
 * public
 * private
 * protected
 * getters/setters
 * static
 * abstract
 */

// 类继承接口
interface IPerson {
  readonly name: string;
}

class Person implements IPerson {
  // 公开 和不加修饰符 效果一样 表示在任意位置都可以访问
  public nickname: string;
  // 只读 只能读取 不能赋值
  readonly name: string = '花匠';
  // 受保护的 protected成员在派生类中仍然可以访问
  protected gender: string;
  // 私有 不能在声明它的类的外部访问
  private _age: number;
  constructor(nickname: string, gender: string, age: number) {
    this.nickname = nickname;
    this.gender = gender;
    this._age = age;
  }
  static sayHi() {
    console.log(this.name);
  }
  // getters/setters 存取器件
  set age(age: number) {
    this._age = age;
  }
  get age(): number {
    return this._age;
  }
}

const person = new Person('nanshu', 'man', 18);
person.name;
person.nickname;

// person.gender;  Property 'gender' is protected and only accessible within class 'Person' and its subclasses.
// stu.age;  Property 'age' is private and only accessible within class 'Student'

class Student extends Person {
  constructor(nickname: string, gender: string, age: number) {
    super(nickname, gender, age);
    // super.age; Property 'age' is private and only accessible within class 'Person'.
    super.name;
    super.nickname;
    super.gender;
  }
}

// 抽象类可以包含成员的实现细节 并且可以包含访问修饰符
abstract class Animal {
  constructor(public name: string) {
    this.name = name;
  }
  abstract makeSound(): void;
  move(): void {
    console.log('moving...');
  }
}

class Dog extends Animal {
  makeSound() {
    console.log('make sound');
  }
}

const dog = new Dog('小花');
dog.name; // 小花
dog.move(); // moving
dog.move(); // make sound

// 类当作接口使用
class Point {
  x: number;
  y: number;
}
const pointA: Point = { x: 7, y: 10 };

interface Point3D extends Point {
  z: number;
}

const pointB: Point3D = { x: 7, y: 1, z: 10 };
```

### 泛型

```ts
// 泛型函数
function echo<T>(arg: T): T {
  return arg;
}

// 泛型类
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

// 泛型约束

function loggingIdentity1<T>(arg: T): T {
  // console.log(arg.length);  Property 'length' does not exist on type 'T'.
  return arg;
}

interface Lengthwise {
  length: number;
}

function loggingIdentity2<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

### 接口

```ts
/**
 * 无法声明基本数据类型 只能声明对象
 * 最简单判断该用readonly还是const的方法是看要把它做为变量使用还是做为一个属性。
 * 做为变量使用的话用const，若做为属性则使用readonly
 */

interface IPerson {
  name: string;
  height?: number;
  readonly age: number;
}

//  多个interface会合并
interface IPerson {
  weight: number;
}

const person: IPerson = {
  name: 'nanshu',
  height: 181,
  weight: 140,
  age: 18,
};

// 索引签名

// 表示这个对象接受 任意string的键 any的值
interface LooseObj {
  [k: string]: any;
}

// 你也可以设置这个索引签名为只读
interface ReadOnlyLooseObj {
  readonly [k: string]: any;
}

interface LooseArr {
  [k: number]: any;
}

const arr: LooseArr = [1, '1', true];
```

### 类型别名

```ts
// 类型别名用来给类型起一个新的名字
type Name = string;
type NameResolver = () => string;

const nickname: Name = 'nanshu';

// 不同与interface type不能重复命名
// type Name = number;  Duplicate identifier 'Name'.
```
