## 基础数据类型

```ts
/**
 * boolean
 * 用构造函数Boolean创造的是对象不是布尔值
 * 直接使用Boolean可以返回一个布尔值
 */
let isShow: boolean = false;
let isOk: boolean = Boolean(true);
// let isTrue: boolean = new Boolean(false) 错误写法 等号右边是一个对象

/**
 * number
 */
let age: number = 18;

/**
 * string
 */
let username: string = 'chou';

/**
 * array
 * 在类型后面直接跟上[]
 * 使用数组泛型
 */
let list1: number[] = [1, 2, 3, 4, 5];
let list2: Array<number> = [1, 2, 3, 4, 5];

/**
 * tuple
 * 元组类型允许我们表示一个已知元素数量和类型的数组，各元素的类型不必相同
 */
let person: [string, number] = ['chou', 18];

/**
 * null && undefined
 * 默认情况下 undefined 和 null 是所有类型的子类 可以给其他类型赋值
 * 在tsconfig.json中配置strict后 它们就只能赋值给void和它们自己
 */
let u: undefined = undefined;
let n: null = null;
// let myString: string = undefined
// let myNumber: number = undefined
let myVoid: void = undefined;

/**
 * enum
 * 默认情况下 从0开始为元素编号
 * 手动编号后 后面的元素从手动编号的那个值开始
 */
enum myColor {
  Red,
  Green = 2,
  Blue,
}

let color: myColor = myColor.Blue;
console.log(color); // 3 因为我们手动给myColor编号了 Blue为3

// 常量枚举 在编译后不会有任何内容

const enum mouth {
  Jan = 1,
  Feb,
}

// 减少魔法数字
enum STATUS {
  READY = 0,
  OK = 1,
  FAILED = -1,
}

let type;

if (type === STATUS.READY) {
  // TODO
}

if (type === STATUS.OK) {
  // TODO
}

if (type === STATUS.FAILED) {
  // TODO
}

/**
 * any
 * 可以声明那些在编程阶段还不确定的变量类型
 * 可以调用任意方法
 * 可以访问任意属性
 * 变量在声明的时候 未指定其类型 那么它会被识别为任意值类型
 */
let notSure: any = 4;
notSure = 'abc';

let password;
// 等价于
// let password:any

/**
 * void
 * 当一个函数没有返回值时 通常会为其声明返回值为void
 * 只能为void类型的变量 赋予undefined和null
 */
function showName(): void {
  alert('chou');
}

/**
 * never
 * 一般是那些抛出异常的函数
 * never是所有类型的子类
 * never类型没有子类 即使是any类型 也不能给never赋值
 */
function myError(message: string): never {
  throw new Error(message);
}

/**
 * object
 * 除number，string，boolean，symbol，null或undefined之外的类型
 * 在tsconfig.json中 未开启strict时 可以将null赋予object类型的变量
 */
let myObject: object;

myObject = { name: 'chou' };
// myObject = null
```

## 联合类型

```ts
/**
 * 联合类型
 * 用 | 隔开每一个类型
 * 当访问属性或调用方法时 ts不确定该变量最后什么类型 所以只能是公共的类型或方法
 * 联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型
 */

function showNumber(myNumber: string | number): string | number {
  // myNumber.toFixed(3); 错误
  return myNumber.toString(); // 只能使用string和number共有的类型
}
```

## 交叉类型

```ts
interface Props1 {
  className: string;
}

interface Props2 {
  onClick: () => void;
}

// &交叉类型 将多个类型合并为一个类型，新的类型将具有所有类型的特性
const props: Props1 & Props2 = {
  onClick: () => {},
  className: 'luckyship-btn',
};
```

## 类型断言

```ts
/**
 *  类型断言
 *  类型断言好比其他语言中的类型转换
 *  它用在开发者明确知道类型的时候
 *  方法一: <>
 *  方法二: as
 */

const getType = function (val: string | number) {
  if (typeof val === 'string') {
    return val.length;
  } else {
    return (val as number).toFixed(2);
  }
};
```
