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

-. 一共只有五种情况：

1. 全局范围直接使用 this：指向全局对象
1. 函数内调用 this：也指向全局对象
1. 方法内调用 this：指向调用的对象
1. 构造函数内使用 this：指向新创建的对象
1. apply call 显式设置 this：显式设置为函数调用的第一个参数

-. 常见误解一：直接调用函数时，this 指向全局对象，这是语言设计的错误地方！

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

-. 常见误解二：方法的赋值表达式。将一个对象的方法赋值给一个变量，此时新的方法调用，会导致内部的 this 不再指向原对象。

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
