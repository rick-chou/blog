> 项目地址 https://github.com/LuckyChou710/ReStart-FE/tree/main/base/js/decorator

## 起步

```json
{
  "name": "decorator",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "@babel/cli": "^7.13.16",
    "@babel/core": "^7.13.16",
    "@babel/plugin-proposal-class-properties": "^7.13.0",
    "@babel/plugin-proposal-decorators": "^7.13.15",
    "@babel/preset-env": "^7.13.15"
  },
  "scripts": {
    "start": "npx babel src/index.js --out-dir dist && node dist/index.js"
  }
}
```

新建 `package.json` 文件 复制如上内容 然后安装依赖

然后在根目录 创建文件.babelrc 配置如下内容

因为 babel 插件是可拔插的 仅仅安装是不会生效的 还需配置

```json
{
  "presets": ["@babel/preset-env"],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators",
      {
        "legacy": true
      }
    ],
    ["@babel/plugin-proposal-class-properties"]
  ]
}
```

然后新建 src 目录 在里面创建 index.js 文件 随后我们就在这个文件内书写我们的代码

## 修饰类

参数就是被装饰器修饰的类

## 修饰成员变量

有三个参数

target 对于静态成员来说是构造函数 对于实例成员来说是原型对象

name 被修饰成员的名字

descriptor 被修饰成员的属性描述符

具体代码如下

```js
@log1
class MyClass {
  constructor() {
    this.name = 'chou';
  }
  @log2
  getName() {
    console.log(this.name);
  }
  @log2 name = 'chou';
  @log2 static age = 18;
}

/**
 *
 * @param {*} target 被修饰的类
 */
function log1(target) {
  target.prototype.logger = () => console.log(`${target.name}被调用`);
}

/**
 *
 * @param {*} target 对于静态成员来说是构造函数 对于实例成员来说是原型对象
 * @param {*} name 被修饰成员的名字
 * @param {*} descriptor 被修饰成员的属性描述符
 */
function log2(target, name, descriptor) {
  // target.log = () => console.log('log')
  console.log('target:', target, 'name:', name, 'descriptor:', descriptor);
}

const test = new MyClass();
test.logger(); // MyClass 被调用
// test.log()
```