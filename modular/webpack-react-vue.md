项目地址：https://github.com/LuckyChou710/ReStart-FE/tree/main/webpack-demo

## React dev

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

const htmlWebpackPlugin = new HtmlWebpackPlugin({
  template: path.resolve(__dirname, '../public/index.html'),
});

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, '../src/index.js'),
  output: {
    path: path.resolve(__dirname, '../build'),
    filename: 'js/[name].[hash].bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.(mjs|js|jsx)$/,
        exclude: /node_modules/,
        use: ['babel-loader'],
      },
    ],
  },

  plugins: [htmlWebpackPlugin],

  resolve: {
    extensions: ['.mjs', '.js', '.jsx'],
  },
};
```

## React prod

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

const htmlWebpackPlugin = new HtmlWebpackPlugin({
  template: path.resolve(__dirname, '../public/index.html'),
});

module.exports = {
  mode: 'production', // 和开发环境下的配置只是修改了 mode
  entry: path.resolve(__dirname, '../src/index.js'),
  output: {
    path: path.resolve(__dirname, '../build'),
    filename: 'js/[name].[hash].bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.(mjs|js|jsx)$/,
        exclude: /node_modules/,
        use: ['babel-loader'],
      },
    ],
  },

  plugins: [htmlWebpackPlugin, new CleanWebpackPlugin()],

  resolve: {
    extensions: ['.mjs', '.js', '.jsx'],
  },
};
```

## Vue dev

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const VueLoaderPlugin = require('vue-loader/lib/plugin');
const htmlWebpackPlugin = new HtmlWebpackPlugin({
  template: path.resolve(__dirname, '../public/index.html'),
});

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, '../main.js'),
  output: {
    path: path.resolve(__dirname, '../build'),
    filename: 'js/[name].[hash].bundle.js',
  },

  module: {
    rules: [
      {
        test: /\.vue$/,
        exclude: /node_modules/,
        use: ['vue-loader'],
      },
    ],
  },
  plugins: [htmlWebpackPlugin, new VueLoaderPlugin()],
  resolve: {
    extensions: ['.js', '.css', '.vue'],
    alias: {
      vue$: 'vue/dist/vue.esm.js',
    },
  },
};
```

## Vue prod

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const VueLoaderPlugin = require('vue-loader/lib/plugin');
const htmlWebpackPlugin = new HtmlWebpackPlugin({
  template: path.resolve(__dirname, '../public/index.html'),
});

module.exports = {
  mode: 'production', // 和开发环境下的配置只是修改了 mode
  entry: path.resolve(__dirname, '../main.js'),
  output: {
    path: path.resolve(__dirname, '../build'),
    filename: 'js/[name].[hash].bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        exclude: /node_modules/,
        use: ['vue-loader'],
      },
    ],
  },

  plugins: [htmlWebpackPlugin, new CleanWebpackPlugin(), new VueLoaderPlugin()],

  resolve: {
    extensions: ['.vue'],
  },
};
```

## package.json

```json
{
  "name": "webpack-demo",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "@babel/core": "^7.13.16",
    "@babel/preset-env": "^7.13.15",
    "@babel/preset-react": "^7.13.13",
    "babel-loader": "^8.2.2",
    "clean-webpack-plugin": "^4.0.0-alpha.0",
    "html-webpack-plugin": "4.5.0",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "vue": "^2.6.12",
    "vue-loader": "^15.9.6",
    "vue-template-compiler": "^2.6.12",
    "webpack": "4.44.2",
    "webpack-cli": "3",
    "webpack-dev-server": "^3.11.2"
  },
  "scripts": {
    "start:react": "webpack-dev-server --config  ./react/config/webpack.config.dev.js --open",
    "build:react": "webpack --config ./react/config/webpack.config.prod.js",
    "start:vue": "webpack-dev-server --config  ./vue/config/webpack.config.dev.js --open",
    "build:vue": "webpack --config ./vue/config/webpack.config.prod.js"
  }
}
```
