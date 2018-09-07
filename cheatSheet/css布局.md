# css 布局

---

<!-- TOC depthFrom:2 -->

- [水平居中](#水平居中)
- [垂直居中](#垂直居中)
- [绝对定位的居中](#绝对定位的居中)
- [两列布局-左侧固定右侧自适应](#两列布局-左侧固定右侧自适应)
- [两列布局-右侧固定左侧自适应](#两列布局-右侧固定左侧自适应)
- [圣杯布局](#圣杯布局)
- [双飞翼布局](#双飞翼布局)
- [底部自适应](#底部自适应)

<!-- /TOC -->

## 水平居中

```css
.demo {
  text-align: center; /* 法一 */

  margin: 0 auto; /* 法二 */

  position: absolute; /* 法三 */
  left: 50%;
  margin-left: -?px;
}

/* 法四 */
.demo-parent {
  display: flex;
  justify-content: center;
}
```

## 垂直居中

```css
.demo {
  height: 30px; /* 法一 */
  line-height: 30px;

  display: table-cell; /* 法二 */
  vertical-align: middle;

  position: absolute; /* 法三 */
  top: 50%;
  margin-top: -?px;
}

/* 法四 */
.demo-parent {
  display: flex;
  align-items: center;
}
```

## 绝对定位的居中

```css
/* 法一 */
.demo {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* 法二 */
.demo-parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

## 两列布局-左侧固定右侧自适应

1. 方法一：左侧绝对定位，右侧利用 margin-left 偏移

   ```html
   <div class="left"></div>
   <div class="right"></div>
   ```

   ```css
   .left {
     background-color: #2196f3;
     position: absolute;
     width: 200px;
     height: 200px;
   }
   .right {
     background-color: #9ccc65;
     margin-left: 200px;
     height: 300px;
   }
   ```

1. 方法二：左侧浮动，右侧利用 BFC 独立

   ```css
   .left {
     background-color: #2196f3;
     float: left;
     width: 200px;
     height: 200px;
   }
   .right {
     background-color: #9ccc65;
     overflow: hidden;
     height: 300px;
   }
   ```

## 两列布局-右侧固定左侧自适应

1. 固定的右侧的块先写

   ```html
   <div class="right"></div>
   <div class="left"></div>
   ```

   ```css
   .right {
     background-color: #2196f3;
     float: right;
     width: 200px;
     height: 200px;
   }
   .left {
     background-color: #9ccc65;
     overflow: hidden;
     height: 300px;
   }
   ```

## 圣杯布局

1. 思路：container 左右添加 padding，通过调控左右两侧的 position 的相对位置来布局。

1. html 部分

   ```html
   <header>header</header>
   <div class="container">
       <div class="main fl">main</div>
       <div class="left fl">left</div>
       <div class="right fl">right</div>
   </div>
   <footer>footer</footer>
   ```

1. css 部分

   ```css
   * {
     text-align: center;
   }
   header {
     width: 100%;
     height: 50px;
     background-color: darkseagreen;
   }
   footer {
     width: 100%;
     height: 50px;
     background-color: darkslategray;
   }
   /* padding 拉出左右的 aside 部分 */
   .container {
     overflow: hidden;
     padding: 0 300px 0 200px;
   }
   .main {
     width: 100%;
     min-height: 300px;
     background-color: #aaa;
   }
   .left {
     width: 200px;
     height: 100px;
     background-color: blue;
   }
   .right {
     width: 300px;
     height: 200px;
     background-color: darkorchid;
   }

   .fl {
     float: left;
     position: relative;
   }

   /* left调控位置 */
   .left {
     margin-left: -100%;
     left: -210px;
   }
   /* right调控位置 */
   .right {
     margin-left: -300px;
     right: -310px;
   }
   ```

## 双飞翼布局

1. 思路：main 部分先写先加载，然后 main 添加包裹层，控制包裹层来调控布局。

1. html 部分

   ```html
   <header>header</header>
   <div class="container">
       <div class="main fl">
           <div class="main-wrap">main</div>
       </div>
       <div class="left fl">left</div>
       <div class="right fl">right</div>
   </div>
   <footer>footer</footer>
   ```

1. css 部分

   ```css
   * {
     text-align: center;
   }
   header {
     width: 100%;
     height: 50px;
     background-color: darkseagreen;
   }
   footer {
     width: 100%;
     height: 50px;
     background-color: darkslategray;
   }
   .container {
     overflow: hidden;
   }
   .main {
     width: 100%;
     min-height: 300px;
     background-color: #fff;
   }
   .left {
     width: 200px;
     height: 100px;
     background-color: #9ccc65;
   }
   .right {
     width: 300px;
     height: 200px;
     background-color: darkorchid;
   }

   .fl {
     float: left;
   }

   /* 左边距为负的父级盒子的宽度 */
   .left {
     margin-left: -100%;
   }
   /* 左边距为负的自己的宽度 */
   .right {
     margin-left: -300px;
   }
   /* main添加包裹层是为了让左右挡住的部分拉开间距 */
   .main-wrap {
     background-color: #aaa;
     margin-left: 210px;
     margin-right: 310px;
   }
   ```

## 底部自适应

1. 思路：将 main 设为 最小高度 100%，再利用 margin 负值，将 header 和 footer 移到视野区。

1. html 部分

   ```html
   <header>header</header>
   <section>main</section>
   <footer>footer</footer>
   ```

1. css 部分

   ```css
   * {
     margin: 0;
     padding: 0;
     box-sizing: border-box;
   }
   html,
   body {
     height: 100%;
   }
   header {
     height: 100px;
     background: #aaa;
     margin-bottom: -100px;
   }
   footer {
     height: 150px;
     background: #666;
     margin-top: -150px;
   }
   section {
     min-height: 100%;
     padding: 100px 0 150px;
   }
   ```
