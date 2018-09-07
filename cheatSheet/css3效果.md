# css3 效果

---

<!-- TOC depthFrom:2 -->

- [大坑](#大坑)
- [手风琴](#手风琴)
- [点控式幻灯片切换](#点控式幻灯片切换)
- [书本阴影](#书本阴影)
- [立方体(cube)](#立方体cube)
- [卡片切换(flip card)](#卡片切换flip-card)
- [旋转木马(carousel)](#旋转木马carousel)

<!-- /TOC -->

## 大坑

- transform 多个变换的书写顺序影响效果！先旋转后移动时，旋转后坐标轴就发生了变化。坐标轴是定位在元素上的！

## 手风琴

> 思路：利用 transition 属性，在 hover 时改变 width 属性，显示对应的图片。

```html
<ul class="demo">
    <li><a href="#"><img src="http://nec.netease.com/img/m/1.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/2.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/3.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/4.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/5.jpg" alt=""/></a></li>
</ul>

<ul class="demo demo-1">
    <li><a href="#"><img src="http://nec.netease.com/img/m/1.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/2.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/3.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/4.jpg" alt=""/></a></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/5.jpg" alt=""/></a></li>
</ul>
```

```css
ul {
  list-style: none;
}
.demo {
  overflow: hidden;
  width: 600px;
  height: 250px;
}
.demo li {
  float: left;
  width: 120px;
  height: 100%;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  transition: width 0.5s;
}
.demo:hover li:not(:hover) {
  width: 50px;
}
.demo li:hover {
  width: 400px;
}

/* 包含初始展开 */
.demo-1 li {
  width: 50px;
}
.demo-1 li:nth-of-type(3) {
  width: 400px;
}
```

## 点控式幻灯片切换

> 思路：利用 transition 属性，在 hover 时改变 opacity 和 transform 属性，显示对应的图片。

```html
<ul class="demo">
    <li></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/1.jpg" alt=""/></a></li>
    <li></li>
    <li></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/2.jpg" alt=""/></a></li>
    <li></li>
    <li></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/3.jpg" alt=""/></a></li>
    <li></li>
    <li></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/4.jpg" alt=""/></a></li>
    <li></li>
    <li></li>
    <li><a href="#"><img src="http://nec.netease.com/img/m/5.jpg" alt=""/></a></li>
    <li></li>
</ul>
```

```css
/* 初始化 */
* {
  margin: 0;
  padding: 0;
}
ul {
  list-style: none;
}

.demo {
  position: relative;
  overflow: hidden;
  width: 400px;
  height: 250px;
}

/* 图片绝对定位 */
.demo li:nth-of-type(3n + 2) {
  position: absolute;
  transition: opacity 0.5s, transform 0.5s;
}

/* 点 */
.demo li:nth-of-type(3n + 1),
.demo li:nth-of-type(3n + 3) {
  position: relative;
  z-index: 5;
  float: left;
  width: 10px;
  height: 10px;
  margin: 230px 10px 0 0;
  border-radius: 50%;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.5) inset;
  background: rgba(0, 0, 0, 0.2);
}
.demo li:nth-of-type(3n + 3) {
  z-index: 4;
  margin-left: -20px;
}
.demo li:first-of-type {
  margin-left: 155px;
}

/* 初始hover，所有图片隐藏 */
.demo:hover li:nth-of-type(3n + 2) {
  opacity: 0;
  transform: scale(1.2);
}

/* 图片被hover时 */
.demo li:nth-of-type(2),
.demo li:nth-of-type(3n + 2):hover,
.demo li:nth-of-type(3n + 1):hover + li {
  z-index: 2;
  opacity: 1;
  transform: scale(1);
}

/* 点被hover时 */
.demo li:nth-of-type(3n + 1):hover,
.demo li:nth-of-type(3n + 2):hover + li {
  background: rgba(0, 99, 177, 0.6);
}
```

## 书本阴影

```html
<div class="drop-shadow">
  <div class="vertical-line"></div>
</div>
```

```css
.drop-shadow {
  position: relative;
  width: 300px;
  height: 200px;
  background: #ccc;
}
.vertical-line {
  position: relative;
  height: 96%;
  width: 0;
  top: 2%;
  margin: 0 auto;
  border: 1px dashed #808080;
}

.drop-shadow::before,
.drop-shadow::after {
  content: '';
  position: absolute;
  z-index: -1;
  bottom: 10px;
  left: 10px;
  width: 50%;
  height: 10%;
  box-shadow: 0 20px 30px rgba(125, 125, 125, 0.9);
  transform: rotate(-5deg);
}
.drop-shadow::after {
  right: 10px;
  left: auto;
  transform: rotate(5deg);
}
```

## 立方体(cube)

> 父元素上设置 `perspective: 800px` 为整个舞台透视，子元素设置 `transform: perspective(800px)` 为自身透视效果。

```html
<section class="cube">
  <main>
    <div>float</div>
    <div>back</div>
    <div>left</div>
    <div>right</div>
    <div>top</div>
    <div>bottom</div>
  </main>

  <button>rotate</button>
</section>
```

```scss
.cube {
  position: relative;
  width: 200px;
  height: 200px;
  margin: 0 auto;
  border: 1px solid #ccc;
  perspective: 400px; // 舞台添加视距，搭建 3D 透视效果

  main {
    position: absolute; // 保证动画中心在 cube 的中心
    width: 100%;
    height: 100%;
    transform-style: preserve-3d; // 子元素 3D 效果
    transform: translateZ(-100px); // 正面被拉向Z轴100px，初始效果再拉回去

    div {
      position: absolute;
      width: 200px;
      height: 200px;
      color: #fff;
      line-height: 200px;
      backface-visibility: visible;
      text: {
        align: center;
        shadow: -5px 5px 5px #37474f;
      }
      font: {
        size: 50px;
        weight: bold;
        variant: small-caps;
      }
    }
    @for $i from 1 through 6 {
      div:nth-of-type(#{$i}) {
        background: hsla($i * 60, 50%, 50%, 0.5);
      }
    }

    // 此处大坑：先旋转，坐标轴也会动，这样就全部都是向Z轴拉动半个盒子宽
    div:nth-of-type(1) {
      transform: rotateY(0deg) translateZ(100px);
    }
    div:nth-of-type(2) {
      transform: rotateY(180deg) translateZ(100px);
    }
    div:nth-of-type(3) {
      transform: rotateY(-90deg) translateZ(100px);
    }
    div:nth-of-type(4) {
      transform: rotateY(90deg) translateZ(100px);
    }
    div:nth-of-type(5) {
      transform: rotateX(-90deg) translateZ(100px);
    }
    div:nth-of-type(6) {
      transform: rotateX(90deg) translateZ(100px);
    }
  }
}

button {
  margin: 250px auto 0 70px;
}

.rotate {
  animation: spinCube 8s infinite;
}
@keyframes spinCube {
  0% {
    transform: rotateX(0deg) rotateY(0deg);
  }
  100% {
    transform: rotateX(360deg) rotateY(360deg);
  }
}
```

```js
const cube = document.querySelector('main');
const btn = document.querySelector('button');
function handleClick() {
  cube.classList.toggle('rotate');
}
btn.addEventListener('click', handleClick, false);
```

## 卡片切换(flip card)

```html
<section class="flipCard">
  <main>
    <div>1</div>
    <div>2</div>
  </main>

  <button>flip card</button>
</section>
```

```scss
.flipCard {
  position: relative;
  width: 200px;
  height: 250px;
  margin: 0 auto;
  border: 1px solid #ccc;
  perspective: 400px;

  main {
    position: absolute;
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
    transform-origin: right center;
    transition: transform 1s;

    div {
      position: absolute;
      width: 200px;
      height: 250px;
      color: #fff;
      line-height: 250px;
      backface-visibility: hidden;
      text: {
        align: center;
        shadow: -5px 5px 5px #37474f;
      }
      font: {
        size: 100px;
        weight: bold;
        variant: small-caps;
      }
    }
    div:nth-of-type(1) {
      background: #f44336;
    }
    div:nth-of-type(2) {
      background: #1976d2;
      transform: rotateY(180deg);
    }
  }

  button {
    margin: 270px 0 0 60px;
  }
}

.flip {
  transform: translateX(-100%) rotateY(-180deg);
}
```

```js
const card = document.querySelector('main');
const btn = document.querySelector('button');
function handleFilpCard() {
  card.classList.toggle('flip');
}
btn.addEventListener('click', handleFilpCard, false);
```

## 旋转木马(carousel)

```html
<section class="carousel">
  <main>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>6</div>
  </main>
</section>

<section>
  <button data-increment="1">prev</button>
  <button data-increment="-1">next</button>
</section>
```

```scss
.carousel {
  position: relative;
  width: 200px;
  height: 100px;
  margin: 0 auto;
  border: 1px solid #ccc;
  perspective: -100px;
  overflow: hidden;

  main {
    position: absolute;
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
    transform: translateZ(-173px);
    transition: transform 0.5s;

    div {
      position: absolute;
      width: 200px;
      height: 100px;
      color: #fff;
      line-height: 100px;
      backface-visibility: hidden;
      text: {
        align: center;
        shadow: -5px 5px 5px #37474f;
      }
      font: {
        size: 50px;
        weight: bold;
        variant: small-caps;
      }
    }
    @for $i from 1 through 6 {
      div:nth-of-type(#{$i}) {
        background: hsla($i * 60, 50%, 50%, 0.5);
        transform: rotateY(#{($i - 1) * 60}deg) translateZ(173px);
      }
    }
  }
}

button:first-of-type {
  margin: 10px 0 0 100px;
}
```

```js
const carousel = document.querySelector('main');
const btns = document.querySelectorAll('button');
let theta = 0;
function handleClick(e) {
  let increment = parseInt(e.target.getAttribute('data-increment'));
  theta += 60 * increment;
  carousel.style.transform = `translateZ(-173px) rotateY(${theta}deg)`;
}
btns.forEach(b => b.addEventListener('click', handleClick, false));
```
