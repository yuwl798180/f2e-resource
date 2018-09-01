<!-- TOC -->

- [对象](#对象)
  - [数字字面量当作对象使用的方法](#数字字面量当作对象使用的方法)
  - [删除对象的指定属性](#删除对象的指定属性)
  - [对象类型的检测](#对象类型的检测)
  - [对象类型转换](#对象类型转换)
  - [对象的原型链继承](#对象的原型链继承)
  - [对象的属性遍历](#对象的属性遍历)
- [函数](#函数)
  - [this 的工作原理](#this-的工作原理)
  - [函数内变量名解析顺序](#函数内变量名解析顺序)
- [ES6 新增](#es6-新增)
  - [可迭代协议与迭代器协议](#可迭代协议与迭代器协议)

<!-- /TOC -->

## 对象

### 数字字面量当作对象使用的方法

除了 `null` `undefined` 其余所有变量都可以当作对象使用。数字的字面量也可以，但是使用时要注意。

```js
6.toString(); // 出错：SyntaxError，点操作符被解析为浮点数字面值的一部分
// 正确使用
6..toString();   // 第二个点号可以正常解析
6 .toString();   // 注意点号前面的空格
(6).toString();  // 6 先被计算
6['toString']()  // 中括号来访问对象属性
```

### 删除对象的指定属性

删除对象的指定属性，需要使用 `delele` 操作符，值改为 `undefined` 或 `null` 仅仅是移除了属性和值的关联。

### 对象类型的检测

```js
var classTest = obj => Object.prototype.toString.call(obj).slice(8, -1);
classTest(new Number(12)); // Number
```

### 对象类型转换

```js
// 下面的比较结果是：true
new Number(10) == 10; // Number.toString() 返回的字符串被再次转换为数字

10 == '10'; // 字符串被转换为数字
10 == '+10 '; // 同上
10 == '010'; // 同上
isNaN(null) == false; // null 被转换为数字 0，0 当然不是一个 NaN

// 下面的比较结果是：false
10 == 010;
10 == '-10';

// 将一个值加上空字符串可以轻松转换为字符串类型。
'' + 10 === '10'; // true

// 使用一元的加号操作符，可以把字符串转换为数字。
+'10' === 10; // true
```

### 对象的原型链继承

```js
function Super(value) {
  this.value = value;
}
Super.prototype.method = function() {}; // 子原型 = 超实例

function Sub() {}
Sub.prototype = new Super();
Sub.prototype.newMethod = function() {};

Sub.prototype.constructor = Sub; // 原型重新指回去
```

### 对象的属性遍历

for in 遍历原型，不遍历不可枚举
hasOwnProperty 不遍历原型，遍历不可枚举

## 函数

> 函数是 JavaScript 中的一等对象

### this 的工作原理

- 一共只有五种情况：

1. 全局范围直接使用 this：指向全局对象
1. 函数内调用 this：也指向全局对象
1. 方法内调用 this：指向调用的对象
1. 构造函数内使用 this：指向新创建的对象
1. apply call 显式设置 this：显式设置为函数调用的第一个参数

- 常见误解一：直接调用函数时，this 指向全局对象，这是语言设计的错误地方！

```js
Foo.method = function() {
  function test() {
    console.log(this); // this 将会被设置为全局对象
  }
  test();
};

/* 一个常见的误解是 test 中的 this 将会指向 Foo 对象，实际上不是这样子的。
   为了在 test 中获取对 Foo 对象的引用，
   我们需要在 method 函数内部创建一个局部变量指向 Foo 对象。
*/
Foo.method = function() {
  var that = this;
  function test() {
    console.log(that); // 使用 that 来指向 Foo 对象
  }
  test();
};
```

- 常见误解二：方法的赋值表达式。将一个对象的方法赋值给一个变量，此时新的方法调用，会导致内部的 this 不再指向原对象。

```js
var test = someObject.methodTest;
test(); // 内部的 this 不再指向 someObject
```

### 函数内变量名解析顺序

比如函数内需要访问 foo 变量时，会按照下面顺序查找 foo：

1. 当前作用域内是否有 var foo 的定义；
1. 函数形式参数是否有使用 foo 名称的；
1. 函数自身是否叫做 foo；
1. 回溯到上一级作用域，然后从 #1 重新开始。

## ES6 新增

### 可迭代协议与迭代器协议

可迭代协议允许 JavaScript 对象去定义或定制它们的迭代行为, 例如定义在一个 `for..of` 结构中什么值可以被循环（得到）。一些内置类型都是内置的可迭代对象并且有默认的迭代行为, 比如 `Array` `String` `Map` `Set` `TypedArray`, 另一些类型则不是 (比如 Object) 。

当一个对象需要被迭代的时候（比如开始用于一个 for..of 循环中），它的@@iterator 方法被调用并且无参数，然后返回一个用于在迭代中获得值的迭代器。

当一个对象被认为是一个迭代器时，它实现了一个 `next()` 的方法并且拥有以下含义：属性为 `next` 值为一个包含 `done (boolean)` 和 `value` 的对象。

```js
var someArray = [1, 5, 7];
var someArrayValues = someArray.values();

someArrayValues.toString(); // "[object Array Iterator]"
someArrayValues === someArrayValues[Symbol.iterator](); // true

var iterator = someArrayValues[Symbol.iterator]();

iterator + ''; // "[object Array Iterator]"
iterator.next(); // { value: 1, done: false }
iterator.next(); // { value: 5, done: false }
iterator.next(); // { value: 7, done: false }
iterator.next(); // { value: undefined, done: true }
```

```js
// 自定义可迭代对象
var myIterable = {};
myIterable[Symbol.iterator] = function*() {
  yield 1;
  yield 2;
  yield 3;
};
[...myIterable]; // [1, 2, 3]
```

```js
// 生成器式的迭代器
function* idMaker() {
  var index = 0;
  while (true) {
    yield index++;
  }
}

var gen = idMaker();

console.log(gen.next().value); // '0'
console.log(gen.next().value); // '1'
console.log(gen.next().value); // '2'
```
