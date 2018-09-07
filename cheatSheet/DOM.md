# DOM

> Document Object Model

---

<!-- TOC depthFrom:2 -->

- [Node 属性](#node-属性)
  - [Node.nodeName, Node.nodeType（只读）](#nodenodename-nodenodetype只读)
  - [Node.nodeValue（可读可写）](#nodenodevalue可读可写)
  - [Node.textContent（可读可写）](#nodetextcontent可读可写)
  - [其他只可读属性](#其他只可读属性)
- [Node 方法](#node-方法)
- [NodeList 对象](#nodelist-对象)
- [HTMLCollection 对象](#htmlcollection-对象)
- [Element 对象](#element-对象)
  - [盒状模型相关属性](#盒状模型相关属性)
  - [相关节点的属性](#相关节点的属性)
  - [特征相关属性](#特征相关属性)
  - [元素节点方法](#元素节点方法)
  - [其他方法](#其他方法)
- [Text 节点](#text-节点)
- [DocumentFragment 节点](#documentfragment-节点)
- [CSS 操作](#css-操作)
- [CSS 事件](#css-事件)
- [异步监视 DOM 变动 API](#异步监视-dom-变动-api)
- [NodeIterator](#nodeiterator)
- [TreeWalker](#treewalker)

<!-- /TOC -->

# DOM

> 文档对象模型：Document Object Model

## Node 属性

> 所有节点对象都是浏览器内置的 Node 对象的实例，继承了 Node 属性和方法。

### Node.nodeName, Node.nodeType（只读）

| 类型                   | nodeName                 | nodeType |
| ---------------------- | ------------------------ | -------- |
| ELEMENT_NODE           | 大写的 HTML 元素名       | 1        |
| TEXT_NODE              | #text                    | 3        |
| COMMENT_NODE           | #comment                 | 8        |
| DOCUMENT_NODE          | #document                | 9        |
| DOCUMENT_TYPE_NODE     | 等同于 DocumentType.name | 10       |
| DOCUMENT_FRAGMENT_NODE | #document-fragment       | 11       |

### Node.nodeValue（可读可写）

1. 返回一个字符串，表示当前节点本身的文本值，该属性可读写。
1. 只有 Text 节点、Comment 节点有文本值，因此只有这些节点可以返回结果，其他类型的节点一律返回 null。

### Node.textContent（可读可写）

1. 返回当前节点和它的所有后代节点的文本内容,该属性可读写。
1. 读取时自动忽略当前节点内部的 HTML 标签，写入时自动对 HTML 标签转义。

### 其他只可读属性

| 属性                 | 描述                                             |
| -------------------- | ------------------------------------------------ |
| Node.ownerDocument   | 返回顶层文档对象，即 document 对象               |
| Node.parentNode      | 返回节点的父节点                                 |
| Node.parentElement   | 返回当前节点的父 Element 节点                    |
| Node.childNodes      | 返回一个 NodeList 集合，包括当前节点的所有子节点 |
| Node.firstChild      | 返回当前节点的第一个子节点                       |
| Node.lastChild       | 返回当前节点的最后一个子节点                     |
| Node.previousSibling | 返回节点之前紧跟的同级节点                       |
| Node.nextSibling     | 返回节点之后紧跟的同级节点                       |

## Node 方法

| 方法                                      | 描述                                                                                 |
| ----------------------------------------- | ------------------------------------------------------------------------------------ |
| Node.hasChildNodes()                      | 返回一个布尔值，表示当前节点是否有子节点                                             |
| Node.appendChild(aChild)                  | 向节点的子节点列表的结尾添加新的子节点；若是已存在节点，则从原来位置移动到新位置     |
| Node.cloneNode([deep])                    | 克隆节点，为真时拷贝子节点。拷贝该节点的所有属性，但会 Event 方法                    |
| Node.insertBefore(newNode, referenceNode) | 将某个节点插入当前节点内部的指定位置                                                 |
| Node.removeChild(child)                   | 从当前节点移除该子节点                                                               |
| Node.replaceChild(newChild, oldChild)     | 将一个新的节点，替换当前节点的某一个子节点                                           |
| Node.contains(otherNode)                  | 返回一个布尔值，表示参数节点是否为当前节点的后代节点                                 |
| Node.compareDocumentPosition(otherNode)   | 返回一个 7 个比特位的二进制值，表示参数节点与当前节点的关系                          |
| Node.isEqualNode(otherNode)               | 返回一个布尔值，用于检查两个节点是否相等（两个节点的类型相同、属性相同、子节点相同） |
| Node.normalize()                          | 用于清理当前节点内部的所有 Text 节点（去除空的文本节点，合并相邻的文本节点）         |

## NodeList 对象

1. `Node.childNodes`返回的是一个动态集合
1. `document.querySelectorAll()`返回的是一个静态集合
1. 提供 `length` 属性和数字索引，但不能直接使用数组方法
1. 最好使用 `for..of` 遍历

## HTMLCollection 对象

1. 也是节点的集合，返回一个类似数组的对象，都是动态集合
1. `document.links`、`docuement.forms`、`document.images`等属性，返回的都是 `HTMLCollection` 实例对象。
1. 使用 `[]` 索引时，除了填写数字外，还可以使用 `id`、`name` 属性。

## Element 对象

> 网页的 HTML 标签元素

### 盒状模型相关属性

| 属性                                 | 描述                                               |
| ------------------------------------ | -------------------------------------------------- |
| Element.clientHeight                 | 返回元素节点可见部分的高度                         |
| Element.clientWidth                  |
| Element.clientLeft                   | 元素节点左边框的宽度                               |
| Element.clientTop                    |
| Element.scrollHeight                 | 返回元素的总高度                                   |
| Element.scrollWidth                  |
| Element.scrollLeft                   | 读写元素滚动条到元素左边的距离                     |
| Element.scrollTop                    |
| Element.offsetHeight                 | 返回元素的垂直高度                                 |
| Element.offsetWidth                  |
| Element.offsetLeft                   | 返回当前元素左上角相对于最近的定位父节点的水平位移 |
| Element.offsetTop                    |
| Element.getBoundingClientRect().left | 网页元素左上角的视口横坐标                         |
| Element.getBoundingClientRect().top  |

### 相关节点的属性

| 属性                           | 描述                                                                   |
| ------------------------------ | ---------------------------------------------------------------------- |
| Element.children               | 返回动态 HTMLCollection 集合，由当前节点的所有 Element 子节点组成      |
| Element.childElementCount      | 返回当前节点的所有 Element 子节点的数目                                |
| Element.firstElementChild      | 返回第一个 HTML 元素类型的子节点                                       |
| Element.lastElementChild       | 返回最后一个 HTML 元素类型的子节点                                     |
| Element.previousElementSibling | 返回当前 HTML 元素节点的前一个同级 HTML 元素节点                       |
| Element.nextElementSibling     | 返回当前 HTML 元素节点的后一个同级 HTML 元素节点                       |
| Element.offsetParent           | 返回当前 HTML 元素最靠近的、并且 position 属性不等于 static 的上层元素 |

### 特征相关属性

| 属性               | 描述                                                                 |
| ------------------ | -------------------------------------------------------------------- |
| Element.attributes | 返回一个类似数组的**动态**对象，包括所有属性，有 `name` `value` 属性 |
| Element.style      | 用来读写该元素的行内样式信息                                         |
| Element.id         | 返回指定元素的 id 属性（可读写）                                     |
| Element.tagName    | 返回指定元素的大写标签名，与 nodeName 属性的值相等                   |
| Element.innerHTML  | 返回该元素包含的 HTML 代码（可读写）                                 |
| Element.outerHTML  | 返回该元素（包括自身）包含的 HTML 代码（可读写）                     |
| Element.className  | 当前元素节点的 class 属性（可读写），是字符串                        |
| Element.classList  | 当前元素节点的 class 属性（可读写），是类数组对象                    |
| Element.classList  | 有方法：add, remove, toggle, contains, replace                       |

### 元素节点方法

| 方法                                | 描述                                                   |
| ----------------------------------- | ------------------------------------------------------ |
| Element.getAttribute(attributeName) | 读取指定属性                                           |
| Element.setAttribute(name, value)   | 设置指定属性                                           |
| Element.hasAttribute(name)          | 返回一个布尔值，表示当前元素节点是否有指定的属性       |
| Element.removeAttribute(attrName)   | 移除指定属性                                           |
| Element.closest(selectors)          | 返回匹配特定选择器且离当前元素最近的祖先元素（或自身） |

### 其他方法

| 方法                                       | 描述                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| Element.scrollIntoView(boolean)            | 滚动当前元素，进入浏览器的可见区域。默认真（顶部对齐）       |
| Element.getBoundingClientRect()            | 返回一个对象，该对象提供当前元素节点的大小、位置等信息       |
| Element.getClientRects()                   | 返回一个类似数组的对象，里面是当前元素在页面上形成的所有矩形 |
| element.insertAdjacentHTML(position, text) | 解析 HTML 字符串，然后将生成的节点插入 DOM 树的指定位置      |
| Element.remove()                           | 将当前元素节点从 DOM 树删除                                  |
| Element.focus()                            | 将当前页面的焦点转移到指定元素上                             |

## Text 节点

| 属性方法               | 描述                                                  |
| ---------------------- | ----------------------------------------------------- |
| data                   | 等同于 nodeValue 属性，用来设置或读取 Text 节点的内容 |
| length                 | 返回当前 Text 节点的文本长度                          |
| nextElementSibling     | 返回紧跟在当前 Text 节点后面的那个同级 Element 节点   |
| previousElementSibling | 返回当前 Text 节点前面最近的那个 Element 节点         |
| remove()               | 用于移除当前 Text 节点                                |
| wholeText              | 将当前 Text 节点与毗邻的 Text 节点，作为一个整体返回  |
| splitText(offset)      | 将 Text 节点一分为二，参数就是分割位置                |

## DocumentFragment 节点

```js
var docFrag = document.createDocumentFragment();
// or
var docFrag = new DocumentFragment();

// 反转一个指定节点的所有子节点的顺序
function reverse(n) {
  var f = document.createDocumentFragment();
  while (n.lastChild) {
    f.appendChild(n.lastChild);
  }
  n.appendChild(f);
}
```

## CSS 操作

1. `getAttribute`, `setAttribute`, `removeAttribute`
   1. `div.setAttribute('style', 'background-color:red;' + 'border:1px solid black;');`
1. 直接读写元素节点 `style` 属性
   1. 属性值用驼峰写法
   1. 属性值都是字符串，设置时必须包括单位
   1. 有个 `cssText` 属性值，可以整体读写样式，而且不用改成驼峰写法
1. 行内样式 `setProperty` `getPropertyValue` `removeProperty`
1. 最终样式 `window.getComputedStyle(element[, pseudoElt])`
   1. 只读，不能改写
1. 获取伪元素信息
   `var result = window.getComputedStyle(test, ':before').content;`
1. `window.matchMedia`

   1. 检查 CSS 的 mediaQuery 语句

   ```js
   @media all and (max-width: 700px) {
       body {
           background: #ff0;
       }
   }

   var result = window.matchMedia('(max-width: 700px)');

   if(result.maches) {
       var linkElm = document.createElement('link');
       linkElm.setAttribute('rel', 'stylesheet');
       linkElm.setAttribute('type', 'text/css');
       linkElm.setAttribute('href', 'small.css');
       document.head.appendChild(linkElm);
   }

   // 指定回调函数
   mql.addListener(mqCallback);
   // 撤销回调函数
   mql.removeListener(mqCallback);
   function mqCallback(mql) {
       if (mql.matches) {
           // 宽度小于等于700像素
       } else {
           // 宽度大于700像素
       }
   }
   ```

## CSS 事件

1. CSS 动画有以下三个事件
   1. `animationstart` 事件：动画开始时触发。
   1. `animationend` 事件：动画结束时触发。
   1. `animationiteration` 事件：开始新一轮动画循环时触发。
1. CSS 的过渡效果（transition）结束后，触发 transitionEnd 事件。
   1. 具有以下属性：`propertyName` `elapsedTime` `pseudoElement`

## 异步监视 DOM 变动 API

> Mutation Observer

```js
// 构造函数，新建一个观察器实例
// 回调函数的参数：第一个是变动数组，第二个是观察器实例
var observer = new MutationObserver(function(mutations, observer) {
  mutations.forEach(function(mutation) {
    console.log(mutation);
  });
});

// 实例方法一：observe()
// 两个参数：第一个是所要观察的DOM元素，第二个是所要观察的变动类型
// 变动类型有：childList  attributes  characterData  subtree
// options 对象还有属性：attributeOldValue  characterDataOldValue  attributeFilter
var article = document.querySelector('article');
var options = {
  childList: true,
  attributes: true,
};
observer.observe(article, options);

// 实例方法二：disconnect()
observer.disconnect(); // 停止观察

// 实例方法三：takeRecords()
// 保存所有没有被观察器处理的变动，返回变动记录的数组
var changes = mutationObserver.takeRecords(); //
```

```js
// 网页加载的时候，DOM 节点的生成会产生变动记录
// 因此只要观察 DOM 的变动，就能在第一时间触发相关事件
// 因此也就没有必要使用 DOMContentLoaded 事件

// 封装一个监听 DOM 生成的函数
(function(win) {
  'use strict';

  var listeners = [];
  var doc = win.document;
  var MutationObserver = win.MutationObserver || win.WebKitMutationObserver;
  var observer;

  function ready(selector, fn) {
    // 储存选择器和回调函数
    listeners.push({
      selector: selector,
      fn: fn,
    });
    if (!observer) {
      // 监听document变化
      observer = new MutationObserver(check);
      observer.observe(doc.documentElement, {
        childList: true,
        subtree: true,
      });
    }
    // 检查该节点是否已经在DOM中
    check();
  }

  function check() {
    // 检查是否匹配已储存的节点
    for (var i = 0; i < listeners.length; i++) {
      var listener = listeners[i];
      // 检查指定节点是否有匹配
      var elements = doc.querySelectorAll(listener.selector);
      for (var j = 0; j < elements.length; j++) {
        var element = elements[j];
        // 确保回调函数只会对该元素调用一次
        if (!element.ready) {
          element.ready = true;
          // 对该节点调用回调函数
          listener.fn.call(element, element);
        }
      }
    }
  }

  // 对外暴露ready
  win.ready = ready;
})(this);

ready('.foo', function(element) {
  // ...
});
```

## NodeIterator

> 深度优先遍历操作

```js
// 参数一： root 搜索起点
// 参数二： whatToShow 要访问的节点类型
// 参数三： filter 是否接受某种特定节点
var iterator = document.createNodeIterator(root, whatToShow, filter);

// 第一次调用返回根节点，一直到返回 null 表示遍历到最后
iterator.nextNode();
iterator.previousNode();
```

```js
// 访问所有类型节点
var iterator = document.createNodeIterator(document, NodeFilter.SHOW_ALL, null);

// 只显示 p 元素的节点迭代器
var filter = function(node) {
  return node.tagName.toLowerCase() === 'p'
    ? NodeFilter.FILTER_ACCEPT
    : NodeFilter.FILTER_SKIP;
};
var iterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT,
  filter
);
var node = iterator.nextNode();
while (node !== null) {
  console.log(node.textContent);
  node = iterator.nextNode();
}
```

## TreeWalker

> 比 NodeIterator 高级的深度遍历

```js
// 和 NodeIterator 使用差不多
// 除了 nextNode()  previousNode()
// 还有 parentNode() firstChild() lastChild() nextSibling() previousSibling()
// 还有一个 currentNode 属性，可读可写，用来修改继续遍历的起点

var treeWalker = document.createTreeWalker(
  document.body,
  NodeFilter.SHOW_ELEMENT,
  {
    acceptNode: function(node) {
      return NodeFilter.FILTER_ACCEPT;
    },
  }
);
var nodeList = [];
while (treeWalker.nextNode()) nodeList.push(treeWalker.currentNode);
```
