本篇主要记录 📝 一下 scss + css modules + antd 样式修改的问题 以及 `styled-components`这个库的使用

## scss + css modules

这也是我一直使用的方式 所以在这里记录一下

我们可以为对应的组件新建一个 index.module.scss 然后在里面书写 scss 的语法

然后在组件内 我们便可以用模块的方式使用这些 css 样式了

举个 🌰

```scss
.container {
  background: yellow;
}
```

```tsx
import styles from './index.module.scss';

const App = () => {
  return (
    <div className={styles.container}>
      <h1>Header</h1>
    </div>
  );
};

export default App;
```

如果 项目中使用了 TS 的话 还需要为`index.module.scss`定义声明文件

新建`index.module.scss.d.ts`

```ts
export const container: string;
```

当然 如果不想为每一个样式 手动编写声明文件的话

你可以试一下 `typings-for-css-modules-loader`

这个 loader 可以自动的帮助你生成声明文件

### 修改 antd 样式

我们引入一个 antd 的 Button

```jsx
import styles from './index.module.scss';
import { Button } from 'antd';

const App = () => {
  return (
    <div className={styles.container}>
      <h1>Header</h1>
      <Button>Antd</Button>
    </div>
  );
};

export default App;
```

打开开发者调试工具 我们可以看到

![](https://cdn.jsdelivr.net/gh/LuckyChou710/blog-images/react/css/css1.png)

我们可以看到 我们的 container 被加上了 一串 hash 而 antd 的样式名没有加 hash

所以我们要告诉 scss 在我们书写 antd 的样式时 不要加上 hash 这样样式名才会匹配 样式才能生效

我们可以用`:global`来包裹住不想被加上 hash 的样式

```scss
.container {
  color: red;
}

:global {
  .ant-btn {
    width: 300px;
  }
}
```

这样 antd 的样式就可以被我们修改啦 😈

## styled-components

### 基本使用

```js
import styled from 'styled-components';

export const Header = styled.div`
  font-size: 20px;
  color: red;
`;

export const Footer = styled.div`
  color: yellow;
`;
```

用``包裹的语法 是标签字符串

styled.div 这些都是第三方库中内置的方法

通过标签字符串 我们不仅可以调用该方法 而且可以在方法内部 通过某种匹配 得到每一个样式

最后在组件中 我们可以像使用普通组件那样来使用 CSS 样式

```jsx
import React from 'react';
import { Header, Footer } from './style';
export default function App() {
  return (
    <div>
      <Header>我是Header</Header>
      <Footer>我是Footer</Footer>
    </div>
  );
}
```

### 高级用法

#### 添加 attrs 属性

```js
const HYInput = styled.input.attrs({
  placeholder: "请填写密码",
})`
```

#### props 穿透

props 可以被传递给 styled 组件

在组件中 我们传递 props

```jsx
<Header color="blue"></Header>
```

在 styled 组件中 我们可以接收到 props

```javascript
const Header = styled.div`
  color: ${(props) => props.color};
}`;
```

#### 支持伪类和伪元素

```js
export const Footer = styled.div`
  color: yellow;
  &:hover {
    color: blue;
  }
  &::after {
    content: '页脚';
  }
`;
```

#### 支持选择器嵌套

```js
export const Footer = styled.div`
  color: yellow;

  p {
    color: red;
  }
`;
```
