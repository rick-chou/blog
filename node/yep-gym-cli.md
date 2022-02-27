## 现在前面

本文是作者在搭建 CLI 时的一些笔记 适合想要学习 CLI 搭建的萌新们了解整个流程

CLI 的搭建其实并不复杂 因为核心功能在 npm 上已经有了很多成熟的模块了

作者实现的 CLI 是一个带模版编译的类似 CRA 的工具(当然是最基础的功能 😬)

因为是在 node 环境下运行的项目 所以下文的 demo 使用的都是 cjs 模块

好啦 CLI 其实没有什么黑魔法 我们开始吧～～～ 😐😐😐

预览图和代码如下 👇👇👇

## Preview

![](https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/node/cli/YepGym.png)

> 仓库地址 https://github.com/LuckyChou710/code-traveling/tree/main/09-node-training-camp/cli

下文说到的`YepGym xxx`就是在执行 CLI

## 工欲善其事，必先利其器

工欲善其事，必先利其器 首先带大家了解一些 CLI 制作必备的 npm 包

以下皆为最基础的使用方法 具体的用法可去对应的官网查看

PS：有很多包已更新成了 ESM 模块 如果要使用 CJS 模块的话 请安装对应的版本

### commander

> https://www.npmjs.com/package/commander

划重点 📝 commander 是 CLI 搭建中的一个最核心的包

它可以为你的 CLI 命令注入很多参数 例如

- `YepGym create xxx` 可以创建一个模版

- `YepGym help` 可以获取 CLI 的所有命令说明

- ......

```
Options:
  -V, --version   output the version number
  -h, --help      display help for command

Commands:
  create|c        create a project
  help [command]  display help for command
```

通过引入`commander`这个库 就可以快速帮助我们实现这个效果

```js
const { program } = require('commander');

program
  .command('create')
  .alias('c')
  .description('create a project')
  .action(() => {
    // do something
  });

program.parse();
```

### inquirer or prompts

> https://www.npmjs.com/package/inquirer

> https://www.npmjs.com/package/prompts

这两个库都可以在命令行生成一些可供用户选择的交互

例如

```
? Select Compiler (Press <space> to select, <a> to toggle all, <i> to invert sel
ection, and <enter> to proceed)
❯◯ Babel
 ◯ TypeScript
```

```js
const inquirer = require('inquirer');

inquirer
  .prompt([
    {
      type: 'checkbox',
      message: 'Select Compiler',
      name: 'compiler',
      choices: [
        {
          name: 'Babel',
        },
        {
          name: 'TypeScript',
        },
      ],
      validate(answer) {
        if (answer.length < 1) {
          return 'You must choose at least one topping.';
        }

        return true;
      },
    },
  ])
  .then((answers) => {
    console.log(JSON.stringify(answers));
  });
```

通过改变第一个 type 属性 我们可以设置成 input / list / ......

### download-git-repo

> https://www.npmjs.com/package/download-git-repo

直接上代码吧 😛

```js
const download = require('download-git-repo');

download('YepGym/react-v17-template', 'test/tmp', { clone: false }, (err) => {
  console.log(err ? 'Error' : 'Success');
});
```

上述代码就会在当前目前下的 test/temp 下拉去远程仓库 YepGym/react-v17-template 下的代码

更多的用法可以参考上述文档

### ejs

> https://ejs.bootcss.com/

> https://www.npmjs.com/package/ejs

ejs 是一种简洁的模版语法 例如 我们有一份 `package.json`

```json
{
  "name": "<%= projectName %>",
  "description": "<%= projectDescription %>",
  "author": "<%= projectAuthor %>",
  "version": "1.0.0",
  "main": "index.js"
}
```

里面有一些类似变量的地方 用 ejs 语法进行占位

通过 ejs 的编译 就可以将这个变量填充进去 生成最终的文件

用法如下

```js
let template = ejs.compile(str, options);
template(data);
// => 输出渲染后的 HTML 字符串

ejs.render(str, data, options);
// => 输出渲染后的 HTML 字符串

ejs.renderFile(filename, data, options, function (err, str) {
  // str => 输出渲染后的 HTML 字符串
});
```

结合上文的 demo 我们需要编译 package.json 用法如下

```js
const ejs = require('ejs');
const fs = require('fs');

const projectName = 'chou-cli';
const projectDescription = 'description ......';
const projectAuthor = 'chou';

ejs
  .renderFile('./package.json', {
    projectName,
    projectDescription,
    projectAuthor,
  })
  .then((res) => {
    fs.writeFile('./package.json', res, (err) => throw err);
  });
```

`ejs.renderFile` 可以读入一个文件 然后第二个参数可以传递模版里需要的变量

### github api

> https://docs.github.com/en/rest/guides/getting-started-with-the-rest-api

github 为我们提供了 restful api 例如仓库的获取 用户信息的获取等等

具体的文档可以参考官方文档

## 锦上添花

### chalk

> https://www.npmjs.com/package/chalk

chalk 是粉笔的意思 顾名思义 它可以让我们的输出(console)带上颜色

直接通过 chalk.<color>的方法就可以为你的 console 注入颜色啦～～

```js
const chalk = require('chalk');

console.log(chalk.blue.bold('Hello Chou!'));
console.log(chalk.blue.bold.bgWhiteBright('Hello Chou!'));
```

### boxen

> https://www.npmjs.com/package/boxen

例如当某个 npm 包有了新的版本需要升级时 或者我们启动一个项目时 经常会发现下面的效果

```
┌─────────────────────────────────────────────┐
│                                             │
│    New version available 4.5.13 → 5.0.1     │
│   Run yarn global add @vue/cli to update!   │
│                                             │
└─────────────────────────────────────────────┘
```

```js
const boxen = require('boxen');
const chalk = require('chalk');

const upgradeMessage = `New version available ${chalk.magenta(
  '4.5.13'
)} → ${chalk.green('5.0.1')} \nRun ${chalk.yellow(
  'yarn global add @vue/cli'
)} to update!`;

console.log(
  boxen(upgradeMessage, {
    align: 'center',
    borderColor: 'green',
    dimBorder: true,
    padding: 1,
  })
);
```

### ora

> https://www.npmjs.com/package/ora

这个库可以生成一个 loading 的 spinner

```
⠏ Fetching...
```

当我们执行一些耗时的操作时 例如 api 请求时 就可以带上这个库让用户有更好的体验

```js
const ora = require('ora');

const spinner = ora('Fetching...');

spinner.start();

setTimeout(() => {
  spinner.succeed('success');
}, 2000);
```

### figlet

> https://www.npmjs.com/package/figlet

例如这个库 我们可以在终端打印出很好看的文字效果 例如文章开头预览图的效果

```
 __    __              ____                        __
/\ \  /\ \            /\  _`\                     /\ \
\ `\`\\/'/  __   _____\ \ \L\_\  __  __    ___ ___\ \ \
 `\ `\ /' /'__`\/\ '__`\ \ \L_L /\ \/\ \ /' __` __`\ \ \
   `\ \ \/\  __/\ \ \L\ \ \ \/, \ \ \_\ \/\ \/\ \/\ \ \_\
     \ \_\ \____\\ \ ,__/\ \____/\/`____ \ \_\ \_\ \_\/\_\
      \/_/\/____/ \ \ \/  \/___/  `/___/> \/_/\/_/\/_/\/_/
                   \ \_\             /\___/
                    \/_/             \/__/
```

```js
const figlet = require('figlet');

figlet.text(
  'YepGym!',
  {
    font: 'Larry 3D',
    horizontalLayout: 'default',
    verticalLayout: 'default',
    width: 80,
    whitespaceBreak: true,
  },
  (err, data) => {
    if (!err) console.log(data);
  }
);
```

figlet 内置了很多种 font 可供选择

> http://www.figlet.org/examples.html

可以通过这个网址 查看 examples

结合 chalk 和该库 就可以实现五颜六色的 logo sign 啦

### clear

clear 可以用清除你的命令行 功能和你直接在 cmd / bash 输入 clear 的效果一样

当我们在屏幕上有比较多的信息 想要清一下屏的时候 就可以使用这个库

```js

> https://www.npmjs.com/package/clear

const clear = require('clear');

clear();
```

### validate-npm-package-name

> https://www.npmjs.com/package/validate-npm-package-name

在 `vue-cli` 中 就是引入了这个库去判断要创建的项目名是否合法

```js
const validateProjectName = require('validate-npm-package-name');
const result = validateProjectName(' leading-space:and:weirdchars');
console.log(result);
// {
//   validForNewPackages: false,
//   validForOldPackages: false,
//   errors: [
//     'name cannot contain leading or trailing spaces',
//     'name can only contain URL-friendly characters'
//   ]
// }
```

## YepGym

### npm link

首先我们要做的就是将我们的 CLI 命令 在终端执行起来

在项目的 package.json 中添加 bin 字段 配置上你的 CLI 命令和对应的文件路径

```json
  "bin": {
    "YepGym": "./bin/YepGym.js"
  },
```

注意 ⚠️： 该文件的文件头必须加上

```
#! /usr/bin/env node
```

表明该文件要以 node 的环境去运行

然后在项目目录下执行 `npm link` 这个命令就会软链接在全局的 bin 目录下

执行 `npm list -g` 可以看到

```
/opt/homebrew/lib
├── nodemon@2.0.15
├── npm@8.3.1
├── nrm@1.2.5
└── yep-gym@1.0.0 -> package.json下配置的文件位置
```

此时 我们在命令行下执行 YepGym 时 就会去找到对应的文件位置 并以 node 环境运行该文件

### cli 流程

整个项目结构如下 仓库链接 🔗 在文章开头

代码比较简单 感兴趣的可以自己查看 🐶

```
YepGym-CLI
├─ .eslintrc.js
├─ README.md
├─ bin
│  └─ YepGym.js
├─ lib
│  ├─ commander.js
│  ├─ create.js
│  ├─ main.js
│  └─ utils
│     ├─ constants.js
│     ├─ generator.js // 编译模版文件
│     ├─ loadCustomPreset.js // 加载用户自定义配置
│     ├─ loadRemotePreset.js // 加载远程仓库模版
│     ├─ prompt.js // 用户自定义信息输入
│     ├─ request.js  //  github api request
│     └─ sign.js
├─ package.json
└─ yarn.lock
```

在这里 只做一些流程上的说明

1. 通过 loadCustomPreset 去加载用户的一些配置
2. 根据选择的仓库 用 `download-git-repo`去拉取对应的模版到本地
3. 如果是带模版语法的 需要进行模版语法编译的 则再进入 generator 方法 在这里 我使用的是 ejs 模版语法
