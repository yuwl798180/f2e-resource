# sass 基础知识

---

<!-- TOC depthFrom:2 depthTo:2 -->

- [变量](#变量)
- [运算](#运算)
- [嵌套](#嵌套)
- [混合（传参使用）](#混合传参使用)
- [继承（非传参）](#继承非传参)
- [条件判断及循环](#条件判断及循环)
- [函数](#函数)
- [自定义函数](#自定义函数)
- [导入](#导入)

<!-- /TOC -->

| 类型             | 特殊符号                                        |
| ---------------- | ----------------------------------------------- |
| 变量             | `$var` `!dafault` `!global` `#{$var}`           |
| 多值变量         | list 类型：`$px: 5px 10px 20px 30px;`           |
| 多值变量         | map 类型：`$headings: (h1: 2em, h2: 1.5em);`    |
| 多值变量循环     | `nth($px,1)` `each $h, $sizing in $headings`    |
| 运算             | 加减乘除，和 `cal` 不同                         |
| 嵌套             | 父级用`&` 属性用`:` keyframes 用`@at-root`      |
| 混合（传参使用） | `@mixin ff($var...) @include ff(多值参数加...)` |
| 继承（非传参）   | `@extend` `%ir 占位符，不使用不解析`            |
| 条件             | `@if @else if @else`                            |
| 循环             | `@for $i from 1 through 3`                      |
| 函数             | `rgba() darken() random() percentage()`         |
| 自定义函数       | `@function @return`                             |
| 导入             | `@import`                                       |

## 变量

```scss
$fontStack: Helvetica, sans-serif;
$primaryColor: #ccc;

body {
  font-family: $fontStack;
  color: $primaryColor;
}
```

### 默认变量

```scss
$baseLineHeight: 2; // 根据需求覆盖默认值
$baseLineHeight: 1.5 !default;

body {
  line-height: $baseLineHeight;
}
```

### 特殊变量

```scss
$borderDirection: top !default;
$baseFontSize: 12px !default;
$baseLineHeight: 1.5 !default;

//应用于class和属性
.border-#{$borderDirection} {
  border-#{$borderDirection}: 1px solid #ccc;
}

//应用于复杂的属性值
body {
  font: #{$baseFontSize}/#{$baseLineHeight};
}
```

### 多值变量

```scss
//一维数据
$px: 5px 10px 20px 30px;
$px: 5px 10px, 20px 30px;
$px: (5px 10px) (20px 30px);

// eg: list
// 结合 nth(list, index) 函数
$linkColor: #08c #333 !default; //第一个值为默认值，第二个鼠标滑过值
a {
  color: nth($linkColor, 1);
  &:hover {
    color: nth($linkColor, 2);
  }
}

$headings: (
  h1: 2em,
  h2: 1.5em,
  h3: 1.2em,
);
// eg: map
// 结合 @each $var in <list or map> 循环
@each $header, $size in $headings {
  #{$header} {
    font-size: $size;
  }
}
```

### 全局变量

```scss
$fontSize: 12px;
$color: #333;
body {
  $fontSize: 14px;
  $color: #fff !global;
  font-size: $fontSize;
  color: $color;
}
p {
  font-size: $fontSize;
  color: $color;
}
```

```css
body {
  font-size: 14px;
  color: #fff;
}
p {
  font-size: 12px;
  color: #fff;
}
```

## 运算

```scss
.container {
  width: 100%;
}

article[role='main'] {
  float: left;
  width: 600px / 960px * 100%;
}

aside[role='complimentary'] {
  float: right;
  width: 300px / 960px * 100%;
}
```

## 嵌套

1. 选择器嵌套：`&` 表示父级。
1. 属性嵌套：对应属性 `-` 换成 `:`。
1. 跳出嵌套：属性前写 `@at-root`。

```scss
nav {
  ul {
    list-style: none;
    @at-root {
      .child {
        width: 100px;
      }
    }
  }
  a {
    text: {
      decoration: none;
      align: center;
      shadow: -5px 5px 5px #37474f;
    }
    font: {
      size: 50px;
      weight: bold;
      variant: small-caps;
    }
    &:hover {
      background-color: #0097a7;
    }
  }
}

// 跳出嵌套主要用于如：keyframes
.demo {
  animation: motion 3s infinite;

  @at-root {
    @keyframes motion {
      0% {
        transform: translate(0, 0);
      }
      100% {
        transform: translate(100px, 20px);
      }
    }
  }
}
```

```css
nav ul {
  list-style: none;
}
.child {
  width: 100px;
}
nav a {
  text-decoration: none;
  text-align: center;
  text-shadow: -5px 5px 5px #37474f;
  font-size: 50px;
  font-weight: bold;
  font-variant: small-caps;
}
nav a:hover {
  background-color: #0097a7;
}
```

## 混合（传参使用）

```scss
// 多个参数mixin
@mixin horizontal-line($border: 1px dashed #ccc, $padding: 10px) {
  border-bottom: $border;
  padding-top: $padding;
  padding-bottom: $padding;
}
.imgtext-h li {
  @include horizontal-line;
}
.imgtext-h--product li {
  @include horizontal-line($padding: 15px);
}

// 多组值参数mixin：一个参数可以有多组值，如 box-shadow、transition
@mixin box-shadow($shadow...) {
  box-shadow: $shadow;
}
.box {
  border: 1px solid #ccc;
  @include box-shadow(
    0 2px 2px rgba(0, 0, 0, 0.3),
    0 3px 3px rgba(0, 0, 0, 0.3),
    0 4px 4px rgba(0, 0, 0, 0.3)
  );
}
```

## 继承（非传参）

```scss
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  @extend .message;
  border-color: green;
}

.error {
  @extend .message;
  border-color: red;
}
```

```scss
// 使用 %var 占位符，这样就可以不使用不解析，节约空间。
%ir {
  color: transparent;
  text-shadow: none;
  background-color: transparent;
}
%clearfix {
  &:before,
  &:after {
    content: '';
    display: table;
    font: 0/0;
  }
  &:after {
    clear: both;
  }
}

#header {
  h1 {
    @extend %ir;
    width: 300px;
  }
}
.ir {
  @extend %ir;
}
```

编译为：

```css
#header h1,
.ir {
  color: transparent;
  text-shadow: none;
  background-color: transparent;
}

#header h1 {
  width: 300px;
}
```

## 条件判断及循环

```scss
// @if 判断  可以结合 @else if
$type: monster;
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}

// @for 循环
@for $i from 1 through 3 {
  .item-#{$i} {
    width: 2em * $i;
  }
}

// 三目判断
if($condition, $if-true, $if-false)
if(true,1px,2px) => //1px
```

## 函数

```scss
rgba($red, $green, $blue, $alpha)
hsla($hue, $saturation, $lightness, $alpha)

lighten($color, $amount)
darken($color, $amount)
rgba($color, $alpha)

random([$limit])
ceil($number)
floor($number)
percentage($value)
```

## 自定义函数

```scss
$total: 8;

@function column-width($col) {
  @return percentage($col / $total);
}

@for $i from 1 through $total {
  .col-#{$i} {
    width: column-width($i);
  }
}
```

## 导入

当不带后缀名时，整合为一个文件；当有后缀名如 css 时，不整合,效果和普通 css 导入文件一样。

```scss
//_reset.scss
html,
body {
  margin: 0;
  padding: 0;
}

// base.scss
@import 'reset';
@import 'a.css';

body {
  font-size: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```

效果为：

```css
@import url(a.css);

html,
body {
  margin: 0;
  padding: 0;
}

body {
  font-size: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```
