---



React 官网对于 CSS 并没有明确的规范


以下提供两种开发者喜爱的 React CSS 解决方案


### CSS MODULE


首先 我们准备好


```jsx
import React from 'react'
import './css/header.css'
import './css/footer.css'

function Header() {
  return (
    <>
      <div className="header">我是Header</div>
    </>
  )
}

function Footer() {
  return (
    <>
      <div className="footer"}>我是Footer</div>
    </>
  )
}

export default function App() {
  return (
    <div>
      <Header />
      <Footer />
    </div>
  )
}
```


然后是 css 文件


```css
/* header.css */
.header {
  font-size: 22px;
}

h1 {
  font-size: 18px;
}

/* footer.css */
.footer {
  color: #ccc;
}

h1 {
  font-size: 20px;
}
```


这是一段传统的 CSS 引入方案


但是 CSS 是层叠样式表 对于重名的选择器 后面的会覆盖前面的


所以上述两个 css 文件后在引入后 footer.css 后引入会覆盖 header.css 中重名的选择器


这时候 我们我们使用 css modules 就可以解决这种问题


相当于给每一个 css 文件都生成了各自的作用域


- 将所有的 css 文件名 改成 xxx.module.css
- 给引入的 css 文件取名 例如 `import HeaderStyle from './css/header.module.css'`
- 将每一个 class 当成变量来使用 例如 `<div className={FooterStyle.footer}>我是Footer</div>`



这种方案的优点很明显 我们不需要额外的学习成本 使用简单


但是 对于需要动态生成的 css 样式 这一种方案就显得无能为力了


### CSS IN JS


对于这种方案 我们直接一个第三库 `styled-components`


#### 安装


```bash
yarn add styled-components
```


#### 引入


新建 style.js


```javascript
import styled from 'styled-components'
```


#### 使用


```javascript
import styled from 'styled-components'

export const Header = styled.div`
  font-size: 20px;
  color: red;
`

export const Footer = styled.div`
  color: yellow;
`
```


用``包裹的语法 是标签字符串


styled.div 这些都是第三方库中内置的方法


通过标签字符串 我们不仅可以调用该方法 而且可以在方法内部 通过某种匹配 得到每一个样式


最后在组件中 我们可以像使用普通组件那样来使用 CSS 样式


```jsx
import React from 'react'
import { Header, Footer } from './style'
export default function App() {
  return (
    <div>
      <Header>我是Header</Header>
      <Footer>我是Footer</Footer>
    </div>
  )
}
```


#### 高级用法


##### 添加 attrs 属性


```javascript
const HYInput = styled.input.attrs({
  placeholder: "请填写密码",
})`
```


##### props 穿透


props 可以被传递给 styled 组件


在组件中 我们传递props


```jsx
<Header color="blue"></Header>
```


在styled组件中 我们可以接收到props


```javascript
const Header = styled.div`
  color: ${(props) => props.color};
}`
```


##### 支持伪类和伪元素


```javascript
export const Footer = styled.div`
  color: yellow;
  &:hover {
    color: blue;
  }
  &::after {
    content: "页脚"
  }
`
```


##### 支持选择器嵌套


```javascript
export const Footer = styled.div`
  color: yellow;

  p {
    color: red;
  }
`
```
