父元素设置 flex 布局后 子元素的浮动会失效

## 父项属性

- ### flex-direction：设置主轴方向

  - row（默认）

  - column

  - row-reverse

  - column-reverse

- ### justify-content： 设置 ！主轴上！ 子元素的排列方式

  - flex-start（默认）贴着左边对齐

  - flex-end

  - center

  - space-around 平均分配剩余空间

  - space-between 两边贴边 再剩余分配剩余空间

- ### flex-wrap：设置子元素是否换行

  - nowrap （默认） 不换行 放不下会缩子元素

  - wrap

- ### align-items：设置 ！侧轴上 ！ 子元素的排列方式（单行）

  - center

  - flex-start

  - flex-end

- ### align-content：多行（子项出现换行）

  - center

  - flex-start

  - flex-end

  - space-around 平均分配剩余空间

  - space-between 两边贴边 再剩余分配剩余空间

- ### flex-flow：简写

  - flex-flow：row wrap

## 子项属性

flex：<num> 子元素占的份数

align-self：控制子元素自己在**侧轴**上的排列方式

order：<num> 控制盒子的位置 默认都是 0 越小越靠前

## flex 计算

TODO
