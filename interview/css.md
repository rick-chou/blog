## display: none;与 visibility: hidden;的区别

- 联系：它们都能让元素不可见

- 区别：

  - display:none;会让元素完全从渲染树中消失，渲染的时候不占据任何空间；

  - visibility: hidden;不会让元素从渲染树消失，渲染师元素继续占据空间，只是内容不可见

  - display: none;是非继承属性，子孙节点消失由于元素从渲染树消失造成，通过修改子孙节点属性无法显示；

  - visibility: hidden;是继承属性，子孙节点消失由于继承了 hidden，通过设置 visibility: visible;可以让子孙节点显式

  - 修改常规流中元素的 display 通常会造成文档重排。修改 visibility 属性只会造成本元素的重绘。

  - 读屏器不会读取 display: none;元素内容；会读取 visibility: hidden;元素内容

## BFC

块格式化上下文（Block Formatting Context，BFC）是 Web 页面的可视化 CSS 渲染的一部分，

是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。

通俗来讲

- BFC 是一个独立的布局环境，可以理解为一个容器，在这个容器中按照一定规则进行物品摆放，并且不会影响其它环境中的物品。

- 如果一个元素符合触发 BFC 的条件，则 BFC 中的元素布局不受外部影响。

创建 BFC

（1）根元素或包含根元素的元素

（2）浮动元素 float ＝ left|right 或 inherit（≠none）

（3）绝对定位元素 position ＝ absolute 或 fixed

（4）display ＝ inline-block|flex|inline-flex|table-cell 或 table-caption

（5）overflow ＝ hidden|auto 或 scroll(≠visible)

BFC 指的是块级格式化上下文，一个元素形成了 BFC 之后，那么它内部元素产生的布局不会影响到外部元素，

外部元素的布局也不会影响到 BFC 中的内部元素。一个 BFC 就像是一个隔离区域，和其他区域互不影响。

一般来说根元素是一个 BFC 区域，浮动和绝对定位的元素也会形成 BFC，display 属性的值为 inline-block、flex

这些属性时也会创建 BFC。还有就是元素的 overflow 的值不为 visible 时都会创建 BFC。

### BFC 布局与普通文档流布局的区别

普通文档流布局

- 浮动的元素是不会被父元素计算高度

- 相邻两个元素的 margin 会重叠

- margin 会传递给父元素

- 非浮动元素会覆盖浮动元素的位置

### 清除浮动

比较好的方法有

- 给父级元素添加 class

```css
.clearFix:after {
  content: ''; /*设置内容为空*/
  height: 0; /*高度为0*/
  line-height: 0; /*行高为0*/
  display: block; /*将文本转为块级元素*/
  visibility: hidden; /*将元素隐藏*/
  clear: both; /*清除浮动*/
}
.clearFix {
  zoom: 1; /*为了兼容IE*/
}
```

- 父级元素设置 overflow:hidden;触发 BFC

- 父元素内结尾处添加 div 标签并设置 class 为 clear:both

### CSS 权重

!important > 内联式 > id > class > tag

### 6.display:inline-block

多个元素设置为 inline-block 显示在一行时 元素之间会有间隙

解决

- 去掉所有的换行和空格

- 使用 margin 为负值

## postcss 的使用

- PostCSS 提供了一个解析器，它能够将 CSS 解析成抽象语法树

- 通过在 PostCSS 这个平台上，我们能够开发一些插件，来处理我们的 CSS，比如热门的：autoprefixer

## base64 的使用

- 用于减少 HTTP 请求

- 适用于小图片

- base64 的体积约为原图的 4/3

## 外边距重叠

- 两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。

- 两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。

- 两个外边距一正一负时，折叠结果是两者的相加的和。

## IFB

IFC 指的是行级格式化上下文，它有这样的一些布局规则：

（1）行级上下文内部的盒子会在水平方向，一个接一个地放置。

（2）当一行不够的时候会自动切换到下一行。

（3）行级上下文的高度由内部最高的内联盒子的高度决定。

## 媒体查询

```css
@media screen and (width: 600px) {
  body {
    color: red;
  }
}
```

## 如何实现单行／多行文本溢出的省略（...）

```css
/*单行文本溢出*/
p {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

## CSS 中哪些属性可以继承

每个 CSS 属性定义的概述都指出了这个属性是默认继承的，还是默认不继承的。这决定了当你没有为元素的属性指定值时该如何计算值。

当元素的一个继承属性没有指定值时，则取父元素的同属性的计算值。只有文档根元素取该属性的概述中给定的初始值（这里的意思应 该是在该属性本身的定义中的默认值）。

当元素的一个非继承属性（在 Mozilla code 里有时称之为 reset property）没有指定值时，则取属性的初始值 initial v alue（该值在该属性的概述里被指定）。

有继承性的属性：

（1）字体系列属性 font、font-family、font-weight、font-size、font-style、font-variant、font-stretch、font-size-adjust

（2）文本系列属性 text-indent、text-align、text-shadow、line-height、word-spacing、letter-spacing、 text-transform、direction、color

（3）表格布局属性 caption-side border-collapse empty-cells

（4）列表属性 list-style-type、list-style-image、list-style-position、list-style

（5）光标属性 cursor

（6）元素可见性 visibility

（7）还有一些不常用的；speak，page，设置嵌套引用的引号类型 quotes 等属性

注意：当一个属性不是继承属性时，可以使用 inherit 关键字指定一个属性应从父元素继承它的值，inherit 关键字用于显式地 指定继承性，可用于任何继承性/非继承性属性。

每一个属性在定义中都给出了这个属性是否具有继承性，一个具有继承性的属性会在没有指定值的时候，会使用父元素的同属性的值 来作为自己的值。

一般具有继承性的属性有，字体相关的属性，font-size 和 font-weight 等。文本相关的属性，color 和 text-align 等。

表格的一些布局属性、列表属性如 list-style 等。还有光标属性 cursor、元素可见性 visibility。

一个属性不是继承属性的时候，我们也可以通过将它的值设置为 inherit 来使它从父元素那获取同名的属性值来继承。

## ::before 和:after 中双冒号和单冒号有什么区别？解释一下这 2 个伪元素的作用

单冒号（:）用于 CSS3 伪类，双冒号（::）用于 CSS3 伪元素。（伪元素由双冒号和伪元素名称组成）

双冒号是在当前规范中引入的，用于区分伪类和伪元素。不过浏览器需要同时支持旧的已经存在的伪元素写法，

比如:first-line、:first-letter、:before、:after 等，而新的在 CSS3 中引入的伪元素则不允许再支持旧的单冒号的写法。

想让插入的内容出现在其它内容前，使用::before，否者，使用::after；

在代码顺序上，::after 生成的内容也比::before 生成的内容靠后。如果按堆栈视角，::after 生成的内容会在::before 生成的内容之上。

回答：

在 css3 中使用单冒号来表示伪类，用双冒号来表示伪元素。但是为了兼容已有的伪元素的写法，在一些浏览器中也可以使用单冒号来表示伪元素。

伪类一般匹配的是元素的一些特殊状态，如 hover、link 等，而伪元素一般匹配的特殊的位置，比如 after、before 等。
