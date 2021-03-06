|              yarn              |                 npm                  | 命令功能                            |
| :----------------------------: | :----------------------------------: | :---------------------------------- |
|         `yarn install`         |            `npm install`             | 根据`pack.json`安装项目所需的依赖包 |
|  `yarn install --no-lockfile`  |   `npm install --no-package-lock`    | 不读取或生成`yarn.lock`锁文件       |
| `yarn install --pure-lockfile` |                 `--`                 | 不要生成`yarn.lock`锁文件           |
|      `yarn add [package]`      |       `npm install [package]`        | 安装需要的依赖包                    |
|   `yarn add [package] --dev`   |  `npm install [package] --save-dev`  | 开发依赖                            |
|    `yarn add [package] --D`    |  `npm install [package] --save-dev`  | 同上                                |
|  `yarn global add [package]`   |   `npm install [package] --global`   | 全局安装依赖包                      |
|     `yarn global upgrade`      |        `npm update --global`         | 全局更新依赖包                      |
|       `yarn add --force`       |            `npm rebuild`             | 更改包内容后进行重建                |
|    `yarn remove [package]`     |      `npm uninstall [package]`       | 卸载已经安装的依赖包                |
|  `yarn cache clean [package]`  |          `npm cache clean`           | 删除缓存                            |
|         `yarn upgrade`         | `rm -rf node_modules && npm install` | 更新依赖包                          |
|     `yarn version --major`     |         `npm version major`          | 更新依赖包的版本                    |
|     `yarn version --minor`     |         `npm version minor`          | 更新依赖包的版本                    |
|     `yarn version --patch`     |         `npm version patch`          | 更新依赖包的版本                    |
|   `yarn config set registry`   |      `npm config set registry`       | 修改源                              |
