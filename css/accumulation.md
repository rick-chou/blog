## 顶部导航栏吸附

不考虑兼容性的话可以使用 sticky 属性

`position：sticky`

必须给一个 top 值 确定最终吸附的位置

可以配合背景颜色 或 z-index 置顶 来达到效果

如果考虑兼容性的话 需要使用 将元素改为 absolute 然后用 offset 计算元素距离页面顶部的距离

最后添加滚动事件 动态修改元素的 top 属性

## 美化导航栏样式

```css
::-webkit-scrollbar {
  width: 4px;
  height: 4px;
}
::-webkit-scrollbar-track {
  background: #f6f6f6;
  border-radius: 2px;
}
::-webkit-scrollbar-thumb {
  background: #aaa;
  border-radius: 2px;
}
::-webkit-scrollbar-thumb:hover {
  background: #747474;
}
::-webkit-scrollbar-corner {
  background: #f6f6f6;
}
```

## 收藏夹和标题 favicon.ico 制作

<a href=https://tool.lu/favicon>ico 在线制作</a>

在 html 中插入语句

```html
<link rel="icon" href="img/favicon.ico" type="imagex-icon" />
<link rel="shortcut icon" href="img/favicon.ico" type="image/x-icon" />
```

## 链接（文本复制）

如果是输入框，可以通过 select() 方法，选中输入框的文本，然后调用 copy 命令，将文本复制到剪切板

但是 select() 方法只对 `<input>` 和 `<textarea>` 有效

所以采用采用其他元素时，可以借助这两个元素，然后将其“隐藏”，隐藏的意思是不可以对浏览器不可见，

如设置为`visibility：hidden`或`display:none`等都会失效，可以使用定位将其“隐藏”

原理：

`[input].select()`

`document.execCommand("copy")`
