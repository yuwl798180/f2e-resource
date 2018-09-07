# JS 常用函数练习

<!-- TOC depthFrom:2 -->

- [Function.prototype.bind 方法](#functionprototypebind-方法)
- [作用域安全的构造函数](#作用域安全的构造函数)
- [关于**引用类型指针**的工作方式](#关于引用类型指针的工作方式)
- [链式调用函数 add()](#链式调用函数-add)
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

## 关于**引用类型指针**的工作方式

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
