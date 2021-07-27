本文主要罗列一些

- 我在使用 git 时候的问题

- git 的常见命令

一方面做个排坑的梳理 一方面也方便自己以后查询这些命令

## 排坑

### git pull / push 卡住

`git pull / push`卡住的可能性有很多

发生这种问题一般是访问不了 github

这里可以登录 ipaddress.com 查看 github.com 的 ip 然后修改 host 可以借助 switchHosts 快速修改 hosts

### gitee 图床

因为 gitee 国内速度比 github 快 所以博主使用 gitee 作为自己的图床

但是某一次在使用的时候 却发现了跨域问题？？？？？

排查之后发现 是因为图片大于 1M gitee 就需要登录校验身份 所以图片需要小于 1M

## 常用命令

| 描述                      | 命令                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 快速切换到上一个分支      | git checkout -                                               |
| 撤销当前分支的所有修改    | git checkout .                                               |
| 拉取远程分支              | git checkout -b [localbranch]/[remote] [branch]              |
| 查看所有远程分支          | git branch -a                                                |
| 删除远程分支              | git push origin --delete [branch]                            |
| 强制删除分支              | git branch -D [branch]                                       |
| 将 dev 分支和当前分支合并 | git merge dev                                                |
| 查看暂存区的文件          | git ls-files                                                 |
| 删除暂存区里的文件        | git rm --cached [file]                                       |
| 本地分支关联远程分支      | git branch --set-upstream-to [remote]/[branch] [localbranch] |
| 回退版本                  | git reset --hard [id]                                        |

## git commit 规范

良好的 git commit 不仅有良好的可读性 而且有利于生成 change logs 做一些自动化的事情

例如 angular.js 的 官网 https://github.com/angular/angular

在这里 git commit 就非常清晰 不同的 commit 分成了不同的类型 让人一眼就知道这次 commit 对应干了什么

commit 的规范 网上有很多介绍 这里只说一点

Commit message 都包括三个部分：header，body 和 footer

- Header

  - type （必需） commit 的类别

  - scope 影响的范围

  - subject（必需） 简短的说明

- Body 详细的说明

- Footer

| type 的类型 | 描述                                                                             |
| ----------- | -------------------------------------------------------------------------------- |
| feat        | 新增功能                                                                         |
| fix         | bug 的修复                                                                       |
| perf        | 性能优化                                                                         |
| refactor    | 重构代码(既没有新增功能，也没有修复 bug)                                         |
| build       | 主要目的是修改项目构建系统(例如 glup，webpack，rollup 的配置等)的提交            |
| ci          | 主要目的是修改项目继续集成流程(例如 Travis，Jenkins，GitLab CI，Circle 等)的提交 |
| docs        | 文档更新                                                                         |
| style       | 不影响程序逻辑的代码修改(修改空白字符，补全缺失的分号等)                         |
| revert      | 回滚某个更早之前的提交                                                           |
| chore       | 变更构建流程和辅助工具                                                           |
| test        | 新增测试用例或是更新现有测试                                                     |

1. 每个提交都必须使用类型字段前缀，它由一个名词组成，诸如 feat 或 fix，其后接一个可选的作用域字段，以及一个必要的冒号（英文半角）和空格。

2. 当一个提交为应用或类库实现了新特性时，必须使用 feat 类型。

3. 当一个提交为应用修复 bug 时，必须使用 fix 类型。

4. 作用域字段可以跟随在类型字段后面。作用有必须是一个描述某部分代码的名词，并用圆括号包围，例如：fix(parser):

5. 描述字段必须紧接在类型/作用域前缀的空格之后。描述指的是对代码变更的简短总结，例如：fix:array parsing issue when multiplejspaces were contained in string。

6. 在简短描述之后，可以编写更长的提交正文，为代码变更提供额外的上下文信息。正文必须起始于描述字段结束的一个空行后。

7. 在正文结束的一个空行之后，可以编写一行或或多行脚注。脚注必须包含关于提交的元信息，例如：关联的合并请求、Reviewer、破坏性变更、每条元信息一行。

8. 破坏性变更必须标示在正文区域最开始处，或脚注区域中某一行的开始。一个破坏性变更必须包含大写的文本 BREAKING CHANGE，后面紧跟冒号和空格。

9. 在 BREAKING CHANGE:之后必须提供描述，以描述对 API 的变更。例如：BREAKING CHANGE: enviroment variables now take precedence over cofig files。

10. 在提交说明中，可以使用 feat 和 fix 之外的类型。

11. 工具的实现必须不区分大小写地解析构成约定式提交的信息单元，只有 BREAKING CHANGE 必须是大写的。

12. 可以在类型/作用域前缀之后，:之前，附加!字符，以进一步提醒注意破坏性变更。当有!前缀时，正文或脚注内必须包含 BREAKING CHANGE: description

这里主要的话 还是推荐一款插件去帮助我们规范自己的 commit 就是 husky

**1、Git Commit 检测工具链**

```bash
yarn add -D husky @commitlint/config-conventional @commitlint/cli
```

配置 husky 插件（在 package.json 中新增一个 husky 相关配置）

```js
"husky": {
  "hooks": {
    "pre-commit": "npm run lint", // 不需要在Commit时lint，不配置此项
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS" // 提交信息检测
  }
}
```

在项目的根目录下新建一个 commitlint.config.js 文件

**Husky 配置**

```js
module.exports = {
  extends: ['@commitlint/config-conventional'], // 使用@commitlint/config-conventional规范
};
```

以上配置完毕后，如果不按照规范提交是无法提交的。

husky 工具会直接从 Git 命令层面打断你的提交。

请按照规范进行提交

**2、辅助 Git Commit 提交格式化的的工具**

**辅助提交工具**

```bash
yarn add -D commitizen // 本地安装

npm install -g commitizen // 全局安装，全局安装后可以使用 git cz 命令，运行git cz 会帮助我们打开交互式的提交
```

本地项目 commitizen 配置（在 package.json 中）

**cz 命令配置**

```js
"scripts": {
  "cz": "git-cz",
},
"config": {
  "commitizen": {
      "path": "./node_modules/cz-conventional-changelog" // 这个文件是commitizen的内部依赖，里面定义了符合Angular提交规范的相关信息，也会方便我们后续生成changelog.md的日志
    }
}
```

以上配置完毕后，就可以使用 git cz(全局) 或者 npm run cz/yarn cz 帮助我们进行提交了

**3、日志生成与版本号自动控制工具（项目管理者使用，成员了解即可）**

**changelog**

```text
yarn add -D standard-version

在package.json 中的配置

"scripts": {
    "major": "standard-version -r major", // 一个最大的版本升级, 会生成相关的changelog,修改版本号 1.0.0 -> 2.0.0，生成一个Tag
    "minor": "standard-version -r minor", // 中等的版本升级 会生成相关的changelog,修改版本号 1.0.0 -> 1.1.0, 生成一个Tag
    "patch": "standard-version -r patch --skip.tag",// 最小的版本升级 会生成相关的changelog,修改版本号 1.0.0 -> 1.0.1, 跳过生成Tag.
    "init": "standard-version  --first-release --skip.tag" // 首次生成相关的changelog, 不修改版本号, 跳过生成Tag. // 也可以不配置进脚本，用npx standard-version  --first-release --skip.tag 执行
  },
```
