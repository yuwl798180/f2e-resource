# Event

---

<!-- TOC depthFrom:2 -->

- [EventTarget 接口](#eventtarget-接口)
- [监听函数位置](#监听函数位置)
- [事件的传播](#事件的传播)
- [事件代理](#事件代理)
- [Event 对象](#event-对象)
  - [构造函数](#构造函数)
  - [事件属性方法](#事件属性方法)
  - [自定义事件](#自定义事件)
  - [模拟事件](#模拟事件)
- [事件种类](#事件种类)

<!-- /TOC -->

## EventTarget 接口

1. `Element`节点、`document`节点和`window`对象部署了这个接口，`XMLHttpRequest`、`AudioNode`、`AudioContext`等浏览器内置对象也部署了这个接口。

1. `target.addEventListener(type, listener[, useCapture])`：绑定事件的监听函数。

   1. `useCapture` 表示监听函数是否在捕获阶段（capture）触发，默认为 false（监听函数只在冒泡阶段被触发）。
   1. 为同一个事件多次添加同一个监听函数，该函数只会执行一次，多余的添加将自动被去除。
   1. 希望向监听函数传递参数，可以用匿名函数包装一下监听函数。

1. `removeEventListener`：移除事件的监听函数。

   1. 必须与对应的 addEventListener 方法的参数完全一致，而且必须在同一个元素节点，否则无效。

1. `dispatchEvent`：触发事件。
   1. 该方法返回一个布尔值，只要有一个监听函数调用了 Event.preventDefault()，则返回值为 false，否则为 true。

## 监听函数位置

1. HTML 标签的 `on-属性`，属性值是执行的代码，不是监听函数

   1. `<div onload="doSomething()">`
   1. 等价于 `el.setAttribute('onclick', 'foo()');`
   1. `this` 不会指向触发事件的元素节点，指向全局对象

1. Element 节点对象的事件属性，指定监听函数

   1. `div.onclick = foo`
   1. 这个方法指定的监听函数，只会在冒泡阶段触发
   1. 只能指定一个监听函数，后面的会覆盖前面的
   1. 监听函数内部的 `this` 对象指向触发事件的那个节点

1. `addEventListener` 方法，这种最好

   1. `div.addEventListener('click', foo, false)`
   1. 还可以部署在 `window`、`XMLHttpRequest`等对象上面
   1. 监听函数内部的 `this` 对象指向触发事件的那个节点

   ```js
   // 监听事件的时候，不能如下写
   element.addEventListener('click', o.m.bind(o)); // 生成匿名函数，导致无法取消绑定
   element.removeEventListener('click', o.m.bind(o)); // 无法取消绑定

   // 正确写法
   var listener = o.m.bind(o);
   element.addEventListener('click', listener);
   element.removeEventListener('click', listener);
   ```

## 事件的传播

1. 从 window 对象传导到目标节点，称为“捕获阶段”（capture phase）。
1. 在目标节点上触发，称为“目标阶段”（target phase）。
1. 从目标节点传导回 window 对象，称为“冒泡阶段”（bubbling phase）。
1. **注意**，用户点击网页的时候，浏览器总是假定 click 事件的目标节点，就是点击位置的嵌套最深的那个节点。

## 事件代理

事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件

```js
var ul = document.querySelector('ul');

ul.addEventListener('click', function(event) {
  if (event.target.tagName.toLowerCase() === 'li') {
    // some code
  }
});
```

## Event 对象

### 构造函数

```js
// eventInit 有 bubbles cancelable 属性（默认都是false）
event = new Event(typeArg, eventInit);

var ev = new Event('look', {
  bubbles: true,
  cancelable: false,
});
document.dispatchEvent(ev);
```

### 事件属性方法

| 属性方法                         | 描述                                                              |
| -------------------------------- | ----------------------------------------------------------------- |
| event.target                     | 返回触发事件的那个节点                                            |
| event.currentTarget              | 返回事件当前所在的节点，在监听函数中等同于 `this`                 |
| event.type                       | 返回一个字符串，表示事件类型，大小写敏感                          |
| event.bubbles                    | 返回一个布尔值，表示当前事件是否会冒泡                            |
| event.cancelable                 | 返回一个布尔值，表示事件是否可以取消                              |
| event.eventPhase                 | 返回一个整数值，表示事件目前所处的节点(0,1,2,3)                   |
| event.defaultPrevented           | 返回一个布尔值，表示该事件是否调用过 preventDefault 方法          |
| event.timeStamp                  | 返回一个毫秒时间戳，表示事件发生的时间                            |
| event.isTrusted                  | 返回一个布尔值，表示该事件是否为真实用户触发                      |
| event.preventDefault()           | 取消浏览器对当前事件的默认行为                                    |
| event.stopPropagation()          | 阻止事件在 DOM 中继续传播，不包括在当前节点上新定义的事件监听函数 |
| event.stopImmediatePropagation() | 阻止同一个事件的其他监听函数被调用                                |

### 自定义事件

```js
var myEvent = new CustomEvent('myevent', {
  detail: {
    foo: 'bar',
  },
  bubbles: true,
  cancelable: false,
});

el.addEventListener('myevent', function(event) {
  console.log('Hello ' + event.detail.foo);
});

el.dispatchEvent(myEvent);
```

### 模拟事件

```js
// 通过MouseEvent构造函数，模拟触发click鼠标事件
function simulateClick() {
  var event = new MouseEvent('click', {
    bubbles: true,
    cancelable: true,
  });
  var cb = document.getElementById('checkbox');
  cb.dispatchEvent(event);
}
```

## 事件种类

1. 鼠标事件

   1. click, dblclick（mousedown→mouseup→click→dbclick）
   1. mouseup, mousedown, mousemove
   1. mouseenter(节点内触发一次), mouseover(节点内触发多次)
   1. mouseleave(不会冒泡), mouseout(会冒泡)
   1. contextmenu(鼠标右键)

1. MouseEvent 对象

   1. `event = new MouseEvent(typeArg, mouseEventInit);`
   1. 以下属性返回数值标识鼠标键信息：button，buttons
   1. 以下属性与事件位置相关：clientX，clientY，screenX，screenY，movementX，movementY
   1. relatedTarget 属性返回事件的次要相关节点

1. 滚轮事件：`var syntheticEvent = new WheelEvent()`

1. 键盘事件

   1. 有 keydown、keypress、keyup 三个事件
   1. `event = new KeyboardEvent(typeArg, KeyboardEventInit);`
   1. 以下属性返回布尔值表示是否按键：altKey，ctrlKey，shiftKey，metaKey
   1. key 属性返回一个字符串，表示按下的键名。

1. 进度事件

   1. abort 事件：当进度事件被中止时触发。如果发生错误，导致进程中止，不会触发该事件。
   1. error 事件：由于错误导致资源无法加载时触发。
   1. load 事件：进度成功结束时触发。
   1. loadstart 事件：进度开始时触发。
   1. loadend 事件：进度停止时触发，发生顺序排在 error 事件\abort 事件\load 事件后面。
   1. progress 事件：当操作处于进度之中，由传输的数据块不断触发。
   1. timeout 事件：进度超过限时触发。
   1. `progressEvent = new ProgressEvent(type, option);`

1. 拖拉事件

   1. 节点属性设为可拖拉 `draggable="true"`
   1. 节点属性设为可放置 `<div ondragover="event.preventDefault()">`
   1. 一旦某个 Element 节点的 draggable 属性设为 true，就无法再用鼠标选中该节点内部的文字或子节点了。
   1. 拖拉过程尽管鼠标在移动，但是鼠标事件不会触发。
   1. 将文件从操作系统拖拉进浏览器，不会触发 dragStart 和 dragend 事件。
   1. 事件种类
      1. dragstart 事件：拖拉开始时在被拖拉的节点上触发，该事件的 target 属性是被拖拉的节点。通常应该在这个事件的监听函数中，指定拖拉的数据。
      1. drag 事件：拖拉过程中，在被拖拉的节点上持续触发。
      1. dragend 事件：拖拉结束时（释放鼠标键或按下 escape 键）在被拖拉的节点上触发，该事件的 target 属性是被拖拉的节点。
      1. dragenter 事件：拖拉进入当前节点时，在当前节点上触发，该事件的 target 属性是当前节点。通常应该在这个事件的监听函数中，指定是否允许在当前节点放下（drop）拖拉的数据。如果当前节点没有该事件的监听函数，或者监听函数不执行任何操作，就意味着不允许在当前节点放下数据。在视觉上显示拖拉进入当前节点，也是在这个事件的监听函数中设置。
      1. dragover 事件：拖拉到当前节点上方时，在当前节点上持续触发，该事件的 target 属性是当前节点。该事件与 dragenter 事件基本类似，默认会重置当前的拖拉事件的效果（DataTransfer 对象的 dropEffect 属性）为 none，即不允许放下被拖拉的节点，所以如果允许在当前节点 drop 数据，通常会使用 preventDefault 方法，取消重置拖拉效果为 none。
      1. dragleave 事件：拖拉离开当前节点范围时，在当前节点上触发，该事件的 target 属性是当前节点。在视觉上显示拖拉离开当前节点，就在这个事件的监听函数中设置。
      1. drop 事件：被拖拉的节点或选中的文本，释放到目标节点时，在目标节点上触发。注意，如果当前节点不允许 drop，即使在该节点上方松开鼠标键，也不会触发该事件。如果用户按下 Escape 键，取消这个操作，也不会触发该事件。该事件的监听函数负责取出拖拉数据，并进行相关处理。
   1. DataTransfer 对象概述
      1. 所有的拖拉事件都有一个 dataTransfer 属性，用来保存需要传递的数据。这个属性的值是一个 DataTransfer 对象。拖拉的数据保存两方面的数据：数据的种类（又称格式）和数据的值。
   1. DataTransfer 对象的属性
      1. dropEffect, effectAllowed, files, types
   1. DataTransfer 对象的方法
      1. setData(), getData(), clearData(), setDragImage()

1. 触摸事件

   1. 触摸 API 由三个对象组成。
   1. Touch 对象
      1. identifier
      1. screenX，screenY，clientX，clientY，pageX，pageY
      1. radiusX，radiusY，rotationAngle
      1. force
      1. target
   1. TouchList 对象
      1. 类似数组的对象，成员是与某个触摸事件相关的所有触摸点
   1. TouchEvent 对象
      1. changedTouches, targetTouches, touches
   1. 触摸事件的种类
      1. touchstart, touchend, touchmove, touchcancel

1. 表单事件

   1. 表单成员事件：Input 事件，select 事件，change 事件
   1. 表单对象事件：reset 事件，submit 事件

1. 文档事件

   1. beforeunload 事件，unload 事件，load 事件，error 事件，pageshow 事件，pagehide 事件
   1. DOMContentLoaded 事件，readystatechange 事件
   1. scroll 事件，resize 事件
   1. hashchange 事件，popstate 事件
   1. cut 事件，copy 事件，paste 事件
   1. 焦点事件
