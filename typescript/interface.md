```ts
/**
 * 接口
 * 对行为的抽象
 * 定义的变量必须和接口中的变量一致 不能多也不能少
 * 类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以
 *
 * 可选属性 ?
 * 只读属性 readonly
 * 继承接口 extends
 * 类实现接口 implements
 * 可索引的类型
 * 混合类型
 *
 */
interface Person {
  name: string;
  age: number;
  height?: string; // 可选属性
  readonly gender: string; // 只读属性
}

let chou: Person = {
  name: 'chou',
  age: 18,
  gender: 'male',
};

// 继承接口
interface Student extends Person {
  sno: string;
}

let stu: Student = {
  name: 'chou',
  age: 18,
  gender: 'male',
  sno: '001',
};

// 类实现接口
interface Counter {
  x: number;
  y: number;
  add(x: number, y: number): number;
}

class myCounter implements Counter {
  x: number;
  y: number;
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
  add(x: number, y: number): number {
    return x + y;
  }
}

// 可索引的类型
// 表示用number索引去访问属性时 会返回string类型
// 例如 arr = ['chou'] arr[0] = 'chou'
// ts支持两种索引类型 string和number 但是数字索引的返回值必须是字符串索引返回值类型的子类型
interface StringArray {
  [index: number]: string;
}
```
