---
title: JavaScript 事件
date: 2020-08-11
tags:
  - JavaScript
categories:
  - JS篇
---

## JS 事件的三个阶段

### 捕获

### 目标

### 冒泡

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>event</title>
    <style>
      .father {
        width: 500px;
        height: 500px;
        background: #ccc;
      }

      .son {
        width: 200px;
        height: 200px;
        background: #888;
      }
    </style>
  </head>

  <body>
    <div class="father">
      <div class="son"></div>
    </div>

    <script>
      document.querySelector('.father').onclick = (e) => {
        console.log('Father');
        console.log(e.target);
      };
      document.querySelector('.son').onclick = (e) => {
        console.log('Son');
        console.log(e.target);
      };
      document.body.onclick = (e) => {
        console.log('Body');
        console.log(e.target);
      };
      document.documentElement.onclick = (e) => {
        console.log('Html');
        console.log(e.target);
      };
    </script>
  </body>
</html>
```

上述例子 我们创建了两个 div 分别是 父盒子 和 子盒子

效果图如下

<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/event.gif" alt="Event Table" style="zoom:80%;" />

现在 我们要有一个清晰的层级关系

自底向上 分别为 son>father>body>html

现在 我们点击子盒子 触发子盒子的点击事件

我们可以看到 点击事件 像气泡一样自底向上 传递到了顶部

这就是事件冒泡

同理 事件捕获 为自顶向下传递事件

<hr />

addEventListener("click",function,"true")方法

若第三参数为 true,则采用事件捕获。若为 false，则采用事件冒泡。

IE 浏览器不支持事件捕获 只支持事件冒泡

## 阻止事件传播

### 阻止冒泡

在 W3C 中，使用 stopPropagation()方法

在 IE 下使用 cancelBubble = true 方法

## JS 事件代理

JS 事件委托

利用事件冒泡 用父元素来触发每个子元素的事件

我们设想一个场景 有五个 input 输入框

现在的需求是 每当有一个输入框被输入了 我们要知道是哪个输入框

如果不用事件委托 我们也可以很轻松的解决问题 那就是为每个 input 绑定 onchange 事件

可是设想一下 如果我们有大量的输入框呢 手动绑定显然就变得不切实际了

这个时候 我们就可以用到事件委托了 给父元素绑定 onchange 事件

利用事件冒泡 让子元素的事件传递到父元素 最终在父元素触发事件

代码 和 效果图 如下

```html
<ul class="_ul">
  <input type="text" name="" id="1" />
  <br />
  <input type="text" name="" id="2" />
  <br />
  <input type="text" name="" id="3" />
  <br />
  <input type="text" name="" id="4" />
  <br />
  <input type="text" name="" id="5" />
  <br />
</ul>
```

```js
document.querySelector('._ul').onchange = (e) => {
  console.log(e.target);
};
```

<br />

<img src="https://gitee.com/LuckyChou/blog-images/raw/master/js/%E4%BA%8B%E4%BB%B6%E5%A7%94%E6%89%98.gif" alt="Event Table" style="zoom:80%;" />
