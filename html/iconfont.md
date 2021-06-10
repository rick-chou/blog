## iconfont

这里推荐两个 iconfont 的网站

- https://icomoon.io/

- https://www.iconfont.cn/

在网站内选好想要的 icon 后 点击下载 就可以得到如下文件

- iconfont.css

- iconfont.svg

- iconfont.eot

- iconfont.woff

- iconfont.ttf

进入 <a href="https://caniuse.com/">can i use</a> 可以查看这些字体的浏览器兼容情况

<!--more-->

## 使用

将下载的文件引入到项目中 然后可以在入口文件引入 iconfont.css

然后在需要到地方使用 i 标签即可

例如 iconfont.css 中有如下 icon

```css
.icon-test:before {
  content: '\e62a';
}
```

我们使用`<i class="icon-test" />`就可以使用这个 icon 了

然后我们可以用 font 的 css 来控制这个 icon

## 实现一个 Iconfont 组件 （基于 React）

```js
import React from 'react';
import './iconfont.css';
const Iconfont = ({ iconId, onClick }) => {
  return <i className={`icon-${iconId}`} onClick={onClick} />;
};
export default Iconfont;
```

## 扩充原项目的 iconfont

如果原有项目中有 iconfont 的相关文件 为了扩充新字体图标 我们可以登录 https://icomoon.io/

点击 Import Icons 选中 iconfont.svg 再重新下载即可
