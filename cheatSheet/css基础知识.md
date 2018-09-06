# CSS 基础知识

---

<!-- TOC depthFrom:2 depthTo:2 -->

- [属性类别](#属性类别)
- [BFC (box formatting context)](#bfc-box-formatting-context)
- [margin 取负值作用](#margin-取负值作用)
- [行高取百分数和小数的区别](#行高取百分数和小数的区别)
- [文本溢出时显示省略号](#文本溢出时显示省略号)
- [列表、表格、多列](#列表表格多列)
- [Flex](#flex)
- [transform、transition、animation](#transformtransitionanimation)
- [小知识点](#小知识点)

<!-- /TOC -->

## 属性类别

|   类别   | 属性                                                                                     |
| :------: | :--------------------------------------------------------------------------------------- |
| 媒体查询 | device-/width(height) device-/aspect-ratio resolution orientation                        |
| 定位布局 | position 上右下左 z-index display float clear visibility overflow box-sizing             |
|  盒尺寸  | width/height margin/padding/border/outline border(width/style/color radius/shadow/image) |
|   背景   | background opacity                                                                       |
| 字体文本 | color font(style/variant/weight/size/family) text(indent/decoration/transform/shadow)    |
| 字体文本 | text-align vertical-align world-break white-space text-overflow direction writing-mode   |
| 列表表格 | list-style table-layout border-collapse empty-cells caption-side                         |
|   多列   | column-(width/count/gap/rule/span/fill/break)                                            |
|  弹性盒  | flex-(grow/shrink/basis/direction/wrap/) jc/ai/ac align-self order                       |
|   动画   | transform transition animation                                                           |
|   内容   | content quotes cursor resize zoom user-select pointer-events                             |
|   其他   | clip page page-break-before/after                                                        |

## BFC (box formatting context)

### BFC 定义

- BFC(Block formatting context) 直译为"块级格式化上下文"。它是一个独立的渲染区域，只有 Block-level Box 参与， 它规定了内部的 Block-level Box 如何布局，并且与这个区域外部毫不相干。
- BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之也如此。
- 因为 BFC 内部的元素和外部的元素绝对不会互相影响，因此当 BFC 外部存在浮动时，它不应该影响 BFC 内部 Box 的布局，BFC 会通过**变窄**，而不与浮动有重叠。同样的，当 BFC 内部有浮动时，为了不影响外部元素的布局，BFC 计算高度时会包括浮动的高度。避免 margin 重叠也是这样的一个道理。

### BFC 布局规则

- 内部的 Box 会在垂直方向，一个接一个地放置。
- Box 垂直方向的距离由 margin 决定。属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠。
- 每个元素的 margin box 的左边， 与包含块 border box 的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
- BFC 的区域不会与 float box 重叠。
- 计算 BFC 的高度时，浮动元素也参与计算。

### 会触发 BFC 的元素

- 根元素
- position 为 absolute 或 fixed
- display 为 inline-block, table-cell, table-caption, flex, inline-flex
- float 为 left, right
- overflow 为 hidden, sroll, auto

### 常用功能

- 两列自适应布局
  - 当左侧浮动时，右侧的 div 因为和浮动块在一个 BFC 中，所以右侧会包括左侧，与左侧块的左边界重合；
  - 让右侧触发 BFC，就不会与左侧重叠，常用 `overflow: hidden` 的方式。
- 清除内部浮动
  - 因为计算 BFC 的高度时，浮动元素也算；所以当发现内部浮动元素溢出框外，就让父级元素触发 BFC，常用 `overflow: auto` 之类的方式。
- 防止垂直 margin 重叠
  - 同属于一个 BFC 的相邻块 margin 会重叠，所以若是不想重叠，就让其中一个加上一个 div 的包裹层。

## margin 取负值作用

- 上和左方的 margin 负值使该元素向上和左方向移动。
- 下和右方的 margin 负值使该元素下方、右方的元素被拉向该元素。

## 行高取百分数和小数的区别

> `line-hight:120%` 和 `line-hight:1.2` 的区别

- 有单位时，子元素继承父元素计算后的行距。
- 无单位时继承缩放系数，子元素会分别计算各自行距（推荐使用无单位）。

## 文本溢出时显示省略号

1. 单行内敛内容溢出块时显示为 `…`

   ```css
   p {
     width: 200px; /* 非 auto */
     overflow: hidden; /* 非 visible */
     white-space: nowrap; /* 默认 normal */
     text-overflow: ellipsis; /* 默认 clip */
   }
   ```

1. 多行溢出时显示为 `…`

   ```css
   p {
     width: 200px;
     overflow: hidden; /* 超过隐藏 */
     word-break: break-all; /* 自动换行 */
     display: -webkit-box; /* 盒子模型 */
     -webkit-line-clamp: 3; /* 行数 */
     -webkit-box-orient: vertical; /* 排列方式 */
   }
   ```

## 列表、表格、多列

1. 列表 list-style: 3 属性简写 / -type,-position,-image

1. 表格

   ```css
   table-layout: fixed; /* 默认 auto  */
   border-collapse: collapse; /* 默认 separate */
   caption-side: bottom; /* 默认 top */
   empty-cells: hide; /* 默认 show */
   border-spacing: 10px 20px; /* 横向纵向间距 */
   ```

1. 多列

   ```
   列宽列数    columns / -width,-count
   列之间样式  column-rule / -width,-style,-color
   列间隙      column-gap
   横跨       column-span: all
   列高统一   column-fill: balance
   ```

## Flex

1. 容器 / 6

   ```
   flex-flow 简写属性: flex-direction / flex-wrap
     主轴的方向 flex-direction: row / row-reverse / column / column-reverse
     容器单行还是多行排布 flex-wrap: nowrap / wrap / wrap-reverse

   ！JC！容器内子元素在主轴方向的对齐方式: justify-content: flex-start / flex-end / center / space-between / space-around
   ！AI！容器内子元素一行在侧轴方向的对齐方式: align-items: stretch / flex-start / flex-end / center / baseline
   ！AC！容器内子元素多行在侧轴方向的对齐方式: align-content: stretch / flex-start / flex-end / center / space-between / space-around
   ```

1. 子元素 / 6

   ```
   flex  简写属性: flex-grow:0 / flex-shrink:1 / flex-basis:auto
   order: 默认0，改变排序
   align-self: 默认auto，计算父元素的 align-items
   ```

1. flex

   ```
   flex: 0;     则其计算值为 flex: 0 1 0%;
   flex: 1;     则其计算值为 flex: 1 1 0%;
   flex: auto;  则其计算值为 flex: 1 1 auto;
   flex: none;  则其计算值为 flex: 0 0 auto;
   ```

1. flex-grow: 默认 0。

   > 当主轴宽-子项基准值和 M>0 时有效，flex-shrink 无效。每个子项的实际宽=basis+扩展宽。

   ```
   .flex {display:flex;width:1000px;}
   .flex:nth-child(1) {flex:1 4 300px;}   扩展宽度为: 1/(1+2+3)*100px
   .flex:nth-child(2) {flex:2 5 200px;}   扩展宽度为: 2/(1+2+3)*100px
   .flex:nth-child(3) {flex:3 6 400px;}   扩展宽度为: 3/(1+2+3)*100px
   ```

1. flex-shrink: 默认 1。

   > 当主轴宽-子项基准值和 M<0 时有效，flex-grow 无效。每个子项的实际宽=basis-缩减宽。

   ```
   .flex {display:flex;width:600px;}
   .flex:nth-child(1) {flex:1 4 300px;}   缩减宽度为: 4*300/(4*300+5*200+6*400)*300px
   .flex:nth-child(2) {flex:2 5 200px;}   缩减宽度为: 5*200/(4*300+5*200+6*400)*300px
   .flex:nth-child(3) {flex:3 6 400px;}   缩减宽度为: 6*400/(4*300+5*200+6*400)*300px
   ```

1. flex-basis: 默认 auto。

   > 继承块本身设定的大小。当所有 flex 子项的 basis 相加等于父元素的宽度时，-grow 和-shrink 无效。

   ```
   .flex {display:flex;width:900px;}
   .flex:nth-child(1) {flex:1 4 300px;}
   .flex:nth-child(2) {flex:2 5 200px;}
   .flex:nth-child(3) {flex:3 6 400px;}
   ```

## transform、transition、animation

- transform

  ```
  transform: translate / rotate / scale / skew
  transform-origin: 50% 50%
  transform-style: flat / preserve-3d
  perspective: none / 观察者与z=0平面的距离，2倍盒大小适宜
  perspective-origin: 50% 50%
  baceface-visibility: visible / hidden
  ```

- transform-function

  ```
  matrix(a,b,c,d,e,f)
  matrix3d(4*4)

  平移  translateX(x)   translate(x,y)  translate3d(x,y,z)
  旋转  rotateX(angle)  rotate(angle)   rotate3d(x,y,z,angle)
  缩放  scaleX(x)       scale(x,y)      scale3d(x,y,z)
  拉伸  skewX(angle)    skew(x-angle,y-angle)
  透视  perspective(n)  不如直接在舞台上用 perspective, 多3D项目时更真实
  ```

1. transition

   ```
   1. transition / 4属性简写
   1. transition-property
   1. transition-duration
   1. transition-timing-function: cubic-bezier() / ease / linear / steps( ) /
   1. transition-delay
   ```

1. animation

   ```
   1. animation / 8属性简写
   1. animation-name: @keyframes
   1. animation-duration
   1. animation-timing-function: timing-function 作用于每两个关键帧之间，而不是整个动画
   1. animation-delay
   1. animation-iteration-count: 1 / infinite
   1. animation-direction: normal / reverse / alternate / reverse-alternate
   1. animation-play-state: running / paused
   1. animation-fill-mode: none / forwards / backwards / both
   ```

## 小知识点

1. `<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />`
1. box-sizing: content-box / border-box，默认 content-box
1. clip: rect(auto 50px 20px auto)
1. box-shadow: 水平偏移，垂直偏移，阴影模糊，阴影外延，color，inset
1. text-shadow: 水平偏移，垂直偏移，阴影模糊，color
1. text-indent: 适用于块/内联块，有继承性
1. vertical-align: 适用于内联元素与 table-cell 元素
1. 空格的处理方式 white-space: normal | pre | nowrap | pre-wrap | pre-line
1. 内容超过指定容器的边界时是否断行 word-wrap: normal | break-word
1. border-image: 5 属性简写 / source,slice,width,outset,repeat
1. background-image: 不是简写！
1. background: 8 属性简写 / -color,-image,-repeat,-attachment,-position,-origin,-clip,-size
1. background-clip: 当为 text 时，有遮罩效果
1. font: 5 属性 / -style,-weight,-variant,-size,-family `italic,bold,small-caps,14px/20px,arial`
1. text-decoration: 3 属性简写 / -line,-color,-style / 下划线的位置
1. direction: rtl 从右向左显示文本流
1. 书写模式控制：direction unicode-bidi writing-mode
1. writing-mode: horizontal-tb / vertical-rl / vertical-lr
1. cursor: `help pointer wait text move not-allowed url zoom-in zoom-out`
1. resize: `none both horizontal vertical` 用在**text-area**中
1. user-select: none 不允许选择文本
1. 边框立体感 `box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);`
