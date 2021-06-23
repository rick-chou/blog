> 项目地址 https://github.com/LuckyChou710/ReStart-FE/tree/main/node/gulp

## gulp 的基本使用

全局安装 `gulp-cli` 用于在命令行执行 gulp 命令

在项目中安装 `gulp`

```bash
yarn global add gulp-cli
yarn add gulp -D
```

gulp 的任务需要写在根目录下的 `gulpfile.js` 中

### 执行任务

```js
// 基础使用
const task1 = (cb) => {
  setTimeout(() => {
    console.log('任务一执行了');
  }, 1000);
  cb();
};

module.exports = {
  task1,
};
```

然后在命令行输入 task1 即可执行 task1 任务

### 并行 / 串行

```js
// 并行
gulp.parallel(task1, task2);
// 串行
gulp.series(task1, task2),
```

## 构建 css

安装 `gulp-less` `gulp-clean-css`

前者用于转换 less 后者用于压缩 css 注意两者在使用上有先后的依赖关系

```js
const gulp = require('gulp');
const less = require('gulp-less');
const cleanCSS = require('gulp-clean-css');

// 处理less文件
const style = () => {
  return (
    gulp
      .src('src/style/main.less', { base: 'src' })
      // 转换less
      .pipe(less())
      // 压缩
      .pipe(cleanCSS())
      // 重命名
      .pipe(
        rename({
          extname: '.min.css',
        })
      )
      // 写入
      .pipe(gulp.dest('dist'))
  );
};

module.exports = {
  style,
};
```

## 构建 js

安装 `gulp-uglify` `gulp-uglify`

前者用于转换新的语法 后者用于压缩 js 代码

```js
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const babel = require('gulp-babel');

const script = () => {
  return gulp
    .src('src/script/index.js', { base: 'src' })
    .pipe(
      babel({
        presets: ['babel-preset-env'],
      })
    )
    .pipe(uglify())
    .pipe(gulp.dest('dist'));
};

module.exports = {
  script,
};
```

## 构建 html

安装 `gulp-htmlmin` 用于压缩 html 代码

```js
const gulp = require('gulp');
const htmlmin = require('gulp-htmlmin');

const html = () => {
  return gulp
    .src('src/index.html', { base: 'src' })
    .pipe(htmlmin({ collapseWhitespace: true }))
    .pipe(gulp.dest('dist'));
};
module.exports = {
  html,
};
```

## 部署服务

```js
const browserSync = require('browser-sync');
const bs = browserSync.create();

const serve = () => {
  // 监听文件的更改 执行对应的任务
  gulp.watch('src/index.html', html);

  bs.init({
    notify: false,
    // 热更新
    files: 'dist/**',
    server: {
      baseDir: 'dist',
      // 映射node_modules下的文件
      routes: {
        '/node_modules': 'node_modules',
      },
    },
  });
};
```
