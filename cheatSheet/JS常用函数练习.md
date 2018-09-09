# JS 常用函数练习

<!-- TOC depthFrom:2 -->

- [Function.prototype.bind 方法](#functionprototypebind-方法)
- [类型检测函数](#类型检测函数)
- [关于循环和闭包](#关于循环和闭包)
- [作用域安全的构造函数](#作用域安全的构造函数)
- [关于引用类型指针的工作方式](#关于引用类型指针的工作方式)
- [链式调用函数 add()](#链式调用函数-add)
- [利用存取器 get set 实现数据对象和 dom 对象的双向绑定](#利用存取器-get-set-实现数据对象和-dom-对象的双向绑定)
- [遍历节点](#遍历节点)
- [计算鼠标移动速度](#计算鼠标移动速度)
- [遍历对象操作](#遍历对象操作)
- [对象复制与深复制](#对象复制与深复制)
- [动态脚本、样式](#动态脚本样式)
- [错误处理机制](#错误处理机制)
- [数组的空元素变成 undefined](#数组的空元素变成-undefined)
- [数字添加千分位](#数字添加千分位)
- [promisify 函数](#promisify-函数)
- [关于全局变量](#关于全局变量)
- [Promise 改写 xhr](#promise-改写-xhr)

<!-- /TOC -->

## Function.prototype.bind 方法

```js
Function.prototype.bind =
  Function.prototype.bind ||
  function() {
    var fn = this,
      context = [].shift.call(arguments),
      args = [].slice.call(arguments);
    return function() {
      return fn.apply(context, args);
    };
  };

// 改写 f.bind(o) 为 bind(f, o)
var bind = Function.prototype.call.bind(Function.prototype.bind);

// 思路： 从 arr.slice(1, 2)改造
arr.slice(2, 4); // 等价于
Array.prototype.slice.call(arr, 2, 4); // 接着提取 slice 方法
slice = Function.prototype.call.bind(Array.prototype.slice);
slice(arr, 2, 4);
bind = Function.prototype.call.bind(Function.prototype.bind); // 类比提取 bind 方法
join = Function.prototype.call.bind(Array.prototype.join); // 类比提取 join 方法
```

## 类型检测函数

```js
function getClass(o) {
  var str = Object.prototype.toString.call(o);
  return /^\[object (.*)\]$/.exec(str)[1];
}

getClass(null); // Null
```

## 关于循环和闭包

```js
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // 3 3 3
  }, 0);
  console.log(i); // 0 1 2
}
// 0 1 2 3 3 3

// 改成 let ，babel 后
var _loop = function _loop(i) {
  setTimeout(function() {
    console.log(i); // 0 1 2
  }, 0);
  console.log(i); // 0 1 2
};
for (var i = 0; i < 3; i++) {
  _loop(i);
}
// 0 1 2 0 1 2
```

## 作用域安全的构造函数

> 当通过构造函数定义新实例时，忘记 new 会导致实例挂载在全局对象下。

```js
function Person(name, age) {
  if (this instanceof Person) {
    this.name = name;
    this.age = age;
  } else {
    return new Person(name, age);
  }
}

var person1 = new Person('leo', 20);
var person2 = Person('Dave', 22);
```

## 关于引用类型指针的工作方式

```js
var a = {n: 1};
var b = a;
a.x = a = {n: 2}; // 或写成 a = a.x = {n: 2};
console.log(a); // {n: 2}
console.log(b); // {n: 1, x: {n: 2}}
```

```
// 解释
1. a.x = a = {n:2} 先执行a.x，此时a和b均共享{n:1}的栈指针，于是{n:1}变为{n:1,x:undefined}；
2. 接着执行 a = {n:2}，此时a不再和b共享同对象的指针而是指向新对象{n:2}；
3. 下一步执行 a.x = a，此时这两个a已经是完全不一样的东西，前者指向{n:1,x:undefined}，后者指向{n:2}。
   执行这一步，{n:1,x:undefined}这个对象（b指向的对象）变为{n:1,x:a}，而a只是指向{n:2}。
```

## 链式调用函数 add()

```js
function add() {
  var args = [].slice.call(arguments);

  var fn = function() {
    var fnArg = [].slice.call(arguments);
    return add.apply([], args.concat(fnArg));
  };
  fn.valueOf = function() {
    return args.reduce((a, b) => a + b);
  };
  return fn;
}

add(1); // 1
add(1)(2); // 3
add(1, 2, 3)(10); // 16
add(1)(2)(3)(4)(5); // 15
```

## 利用存取器 get set 实现数据对象和 dom 对象的双向绑定

```js
Object.defineProperty(user, 'name', {
  get: function() {
    return document.getElementById('foo').value;
  },
  set: function(newValue) {
    document.getElementById('foo').value = newValue;
  },
  configurable: true,
});
```

## 遍历节点

```js
// 遍历当前节点的所有后代节点
function DOMComb(parent, callback) {
  if (parent.hasChildNodes()) {
    for (var node = parent.firstChild; node; node = node.nextSibling) {
      DOMComb(node, callback);
    }
  }
  callback.call(parent);
}

function printContent() {
  if (this.nodeValue) {
    console.log(this.nodeValue);
  }
}

DOMComb(document.body, printContent);
```

## 计算鼠标移动速度

```js
// 显示每秒移动的像素数量
var previousX;
var previousY;
var previousT;

window.addEventListener('mousemove', function(event) {
  if (
    !(
      previousX === undefined ||
      previousY === undefined ||
      previousT === undefined
    )
  ) {
    var deltaX = event.screenX - previousX;
    var deltaY = event.screenY - previousY;
    var deltaD = Math.sqrt(Math.pow(deltaX, 2) + Math.pow(deltaY, 2));
    var deltaT = event.timeStamp - previousT;
    var deltaV = Math.floor((deltaD / deltaT) * 1000);
    console.log(deltaV + '像素/每秒');
  }

  previousX = event.screenX;
  previousY = event.screenY;
  previousT = event.timeStamp;
});
```

## 遍历对象操作

```js
// 返回对象所有的属性
// `prop in obj` 是判断 prop 是否为 obj 的属性
const getAllProps = obj => {
  const props = [];
  while (obj) {
    Object.getOwnPropertyNames(obj).forEach(
      p => (!props.includes(p) ? props.push(p) : null)
    );
    obj = Object.getPrototypeOf(obj);
  }
  return props;
};

// 返回对象所有的属性，并且是可以枚举的
const getAllEnumProps = obj => {
  const enumProps = [];
  for (let name in obj) {
    enumProps.push(name);
  }
  return enumProps;
};

// 返回对象所有的属性，并且是不可枚举的
const getAllInenumProps = obj =>
  getAllProps(obj).filter(p => !getAllEnumProps(obj).includes(p));

// 返回对象自身所有属性的键名
const getOwnProps = obj => Object.getOwnPropertyNames(obj);

// 返回对象自身的所有属性的键名，并且是可以枚举的
const getOwnEnumProps = obj => Object.keys(obj);

// 返回对象自身的所有属性的键名，并且是不可枚举的
const getOwnInenumProps = obj =>
  Object.getOwnPropertyNames(obj).filter(p => !Object.keys(obj).includes(p));
```

## 对象复制与深复制

```js
// 确保拷贝后的对象，与原对象具有同样的prototype原型对象，同样的属性
// 使用 Object.create(proto[, propertiesObject])
function shallowClone(orig) {
  return Object.create(
    Object.getPrototypeOf(orig),
    Object.getOwnPropertyDescriptors(orig)
  );
}

var a = {ddd:111, get a(){ return 1}};
var b = shallowClone(a);

// 深复制：遍历属性值为对象时，继续浅复制
function deepClone(orig) {
    var keys = Object.getOwnPropertyNames(orig),
        len = keys.length,
        toStr = Object.prototype.toString,
        result = {};
    for(var i = 0; i < len; i++) {
        toStr.call(keys[i]) === '[object Object]' ?
        deepClone(keys[i]) :
        result.keys[i] = orig.keys[i];
        }
    }
    return result = {};
}
```

## 动态脚本、样式

1. defer 属性和 async 属性脚本之间没有依赖关系，就使用 async 属性，如果脚本之间有依赖关系，就使用 defer 属性。如果同时使用 async 和 defer 属性，后者不起作用，浏览器行为由 async 属性决定。

1. 动态脚本

```js
function loadScript(url) {
  var script = document.createElement('script');
  script.type = 'text/javascript';
  script.scr = url;
  // script.async = true;    // 加载的脚本没有相互依赖关系
  script.onload = function() {
    // 添加回调函数
    done();
  };
  script.onerror = function() {
    done(new Error('Failed to load script ' + src));
  };
  document.body.appendChild(script);
}

function loadStyles(url) {
  var link = document.createElement('link');
  link.rel = 'stylesheet';
  link.type = 'text/css';
  link.href = url;
  // document.getElementsByTagName('head')[0].appendChild(link);
  document.head.appendChild(link);
}

loadScript('//a.js');
loadStyles('//b.css');
```

## 错误处理机制

> 在函数头部，使用 try 检测参数类型是否符合标准。利用 图像 ping 将错误记录到服务器。

```js
// 一个典型的 try..catch..finally 场景
openFile();

try {
  writeFile(Data);
} catch (e) {
  handleError(e);
} finally {
  closeFile();
}
```

```js
// try..catch..finally 三者执行顺序
function f() {
  try {
    console.log(0);
    throw new Error('bug');
  } catch (e) {
    console.log(1);
    return true; // 这句原本会延迟到finally代码块结束再执行
    console.log(2); // 不会运行
  } finally {
    console.log(3);
    return false; // 这句会覆盖掉前面那句return
    console.log(4); // 不会运行
  }

  console.log(5); // 不会运行
}

var result = f();
// 0
// 1
// 3

result;
// false
```

```js
// 服务器页面
// params: 严重程度，错误消息
function logError(sev, msg) {
  var img = new Image();
  img.src =
    'log.php?sev=' +
    encodeURIComponent(sev) +
    '&msg=' +
    encodeURIComponent(msg);
}

try {
  mods[i].init();
} catch (ex) {
  logError('nonfatal', 'Module init failed: ' + ex.message);
}
```

## 数组的空元素变成 undefined

空元素与 undefined 的差别在于，数组的 forEach 方法会跳过空元素，但是不会跳过 undefined。因此，遍历内部元素的时候，会得到不同的结果。

```js
const empty2undefined = arr => Array.apply(null, arr);

var arr = ['a', , 'b'];
empty2undefined(arr);
```

## 数字添加千分位

```js
//不考虑非数字
var miliFormat = num => {
  let regexp = /(^|\s)-?\d+(?=\.?\d*($|\s))/g;
  let replaceFunc = /(?=(?!\b)(\d{3})+\.?\b)/g;
  return num.toString().replace(regexp, m => m.replace(replaceFunc, ','));
};

var a = -1234567.123456;
console.log(miliFormat(a));
```

## promisify 函数

```js
var promisify = (func, ctx) => {
  // 返回一个新的function
  return function() {
    // 初始化this作用域
    var ctx = ctx || this;
    // 新方法返回的promise
    return new Promise((resolve, reject) => {
      // 调用原来的非promise方法func，绑定作用域，传参，以及callback（callback为func的最后一个参数）
      func.call(ctx, ...arguments, function() {
        // 将回调函数中的的第一个参数error单独取出
        var args = Array.prototype.map.call(arguments, item => item);
        var err = args.shift();
        // 判断是否有error
        if (err) {
          reject(err);
        } else {
          // 没有error则将后续参数resolve出来
          args = args.length > 1 ? args : args[0];
          resolve(args);
        }
      });
    });
  };
};
```

## 关于全局变量

```js
var a = 2,
  b = 3,
  c = 4;
(function() {
  var a = (b = c = 10);
})();

console.log(a); // 2
console.log(b); // 10
console.log(c); // 10
```

## Promise 改写 xhr

```js
// xhr 例子
const getURL = URL => {
  const xhr = new XMLHttpRequest();
  const promise = new Promise((resolve, reject) => {
    xhr.open('GET', URL, true);
    xhr.onload = function() {
      if (xhr.status === 200) {
        resolve(xhr.response || xhr.responseText);
      } else {
        reject(new Error(xhr.status + ' : ' + xhr.statusText));
      }
    };
    xhr.onerror = function() {
      reject(new Error(xhr.status + ' : ' + xhr.statusText));
    };
    xhr.onabort = function() {
      reject(new Error('abort this request'));
    };
    xhr.send();
  });
  const abort = function() {
    if (xhr.readyState !== XMLHttpRequest.UNSENT) {
      xhr.abort();
    }
  };

  return {
    promise,
    abort,
  };
};

const URL = [
  'http://azu.github.io/promises-book/json/comment.json',
  'http://azu.github.io/promises-book/json/people.json',
];

// 单一请求
var singleReq = getURL(URL[0])
  .promise.then(JSON.parse) // value=>JSON.parse(value)
  .then(console.log)
  .catch(error => console.error(error));

// 多请求
// all 是所有同时进行，全部完成后才then。
// race 也是同时进行，第一个完成就then，但是其他的还在继续，并不会停止。
var multiReqObj = {
  comment: () => getURL(URL[0]).promise.then(JSON.parse),
  people: () => getURL(URL[1]).promise.then(JSON.parse),
};

var multiReq = Promise.all([multiReqObj.comment(), multiReqObj.people()])
  .then(console.log)
  .catch(console.error);

// 多个请求顺序执行时，使用数组的 reduce 方法
var sequenceArr = [multiReqObj.comment, multiReqObj.people];
var sequenceTasks = arr => {
  const recordValue = (results, value) => {
    results.push(value);
    return results;
  };
  const pushValue = recordValue.bind(null, []);
  return arr.reduce(
    (promise, a) => promise.then(a).then(pushValue),
    Promise.resolve()
  );
};
sequenceTasks(sequenceArr)
  .then(console.log)
  .catch(console.error);

// 超时判断，超时就取消xhr
// 利用 race 和 sleep 一起
class TimeoutError extends Error {}
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));
const timeoutPromise = (promise, ms) =>
  Promise.race([
    promise,
    sleep(ms).then(() => {
      throw new TimeoutError('Operation timed out after ' + ms + ' ms');
    }),
  ]);

var timeoutReq = getURL(URL[0]);
timeoutPromise(timeoutReq.promise, 1000)
  .then(JSON.parse)
  .then(console.log)
  .catch(error => {
    if (error instanceof TimeoutError) {
      timeoutReq.abort();
      return console.error(error);
    }
    return console.error(error);
  });
```
