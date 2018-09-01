<!-- TOC -->

- [基础知识](#基础知识)
  - [js 引擎的解释机制](#js-引擎的解释机制)
  - [js 引擎的提升机制](#js-引擎的提升机制)
- [对象](#对象)
  - [数字字面量当作对象使用的方法](#数字字面量当作对象使用的方法)
  - [删除对象的指定属性](#删除对象的指定属性)
  - [对象类型的检测](#对象类型的检测)
  - [对象类型转换](#对象类型转换)
  - [对象的原型链继承](#对象的原型链继承)
  - [对象的属性遍历](#对象的属性遍历)
- [函数](#函数)
  - [this 的工作原理](#this-的工作原理)
  - [new 的过程](#new-的过程)
  - [函数内变量名解析顺序](#函数内变量名解析顺序)
- [ES6 新增](#es6-新增)
  - [可迭代协议与迭代器协议](#可迭代协议与迭代器协议)

<!-- /TOC -->

## 基础知识

### js 引擎的解释机制

1. 对于传统编译型语言来说，编译步骤分为：`词法分析`、`语法分析`、`语义检查`、`代码优化`和`字节生成`。对于 js 解释型语言来说，通过`词法分析`和`语法分析`得到`语法树`后，就可以开始解释执行了。
1. js 是词法作用域（静态作用域），和动态作用域的区别是：

   1. 词法作用域是指在`书写代码`时或者说定义时确定了作用域，关注函数在`何处声明`。词法作用域中的函数遇到既不是形参又不是函数内部定义的局部变量时，会去`函数定义`时的环境查询。
   1. 而动态作用域是在`运行时`确定作用域，关注函数从`何处调用`。动态作用域中的函数遇到既不是形参又不是函数内部定义的局部变量时，到`函数调用`时的环境查询。

   ```js
   // 词法作用域 lexical scope
   function foo() {
     console.log(a); //输出1
   }
   function bar() {
     var a = 2;
     foo();
   }
   var a = 1;
   bar();

   // 动态作用域 dynamic scope
   function foo() {
     console.log(a); //输出2
   }
   function bar() {
     var a = 2;
     foo();
   }
   var a = 1;
   bar();
   ```

1. 但是 js 可以使用 `with eval` 构建动态作用域。
1. 大多语言都是静态作用域和块结构，js 补充了 let 也提供了块结构。静态作用域的语言，基本都是采用`最内嵌作用域规则`，并且控制栈的活动记录中通过`访问链`（access link）维护静态作用域。
1. js 引擎在执行每个函数实例时，都会创建一个`执行环境`（execution context）。执行环境中包含一个`调用对象`（call object）, 调用对象是一个`脚本对象`（scriptObject）结构，用来保存`内部变量表`（varDecls）、`内嵌函数表`（funDecls）、`父级引用列表`（upvalue）等语法分析结构。`内部变量表`和`内嵌函数表`等信息是在语法分析阶段就已经得到，并保存在语法树中。函数实例执行时，会将这些信息从语法树复制到`脚本对象`上。`脚本对象`是与函数相关的一套静态系统，与函数实例的生命周期保持一致。
1. `词法作用域`（lexcical scope）是 js 的作用域机制，还需要理解它的实现方法，这就是`作用域链`（scope chain）。作用域链是一个`命名查询`（name lookup）机制，首先在当前执行环境的 scriptObject 中寻找，没找到则顺着 upvalue 到父级 scriptObject 中寻找，一直 lookup 到`全局调用对象`（global object）。
1. 当一个函数实例执行时，会创建或关联到一个`闭包`（closure）。 `脚本对象`用来静态保存与函数相关的变量表，`闭包`则在执行期动态保存这些变量表及其运行值。`闭包`的生命周期有可能比函数实例长。函数实例在活动引用为空后会自动销毁，`闭包`则要等要数据引用为空后，由 js 引擎回收（有些情况下不会自动回收，就导致了内存泄漏）。
1. 函数内名称解析顺序：比如，当访问函数内的 foo 变量时，JavaScript 会按照下面顺序查找：
   1. 当前作用域内是否有 var foo 的定义；
   1. 函数形式参数是否有使用 foo 名称的；
   1. 函数自身是否叫做 foo；
   1. 回溯到上一级作用域，然后从 #1 重新开始。

### js 引擎的提升机制

1. js 解释器执行代码前，先创建全局对象，进行预解析。
1. js 引擎会优先解析 `var 变量` 和 `function 定义`。
   1. 对于 `var` 定义的变量，无论该变量自身有没有赋值，在预解析阶段都先赋值 `undefined`。意思是 `var` 定义的变量提升，只是预定义变量却不赋值，即该变量值为 `undefined`。
   1. 对于 `function` 定义的函数，进行预解析的时候，不仅是声明了函数而且还定义了函数。但是它存储数据的空间里面，存储的是代码字符串，没有任何意义。后面同名定义的函数会覆盖前面定义的。也不会受到 function 里面的 return 影响。
1. 预解析是不受 `if` 或者其它判断条件影响的。也就是说，即使条件不成立，我们里面只要有 var 或者 function 也会被预解析。
1. 预解析是分段进行的，准确说是分 `<script>` 块进行的。
1. var 定义的变量和函数声明的方式有提升，函数表达式不提升。
1. 在进入执行上下文后，填充 VO 的顺序是: 函数的形参 -> 函数申明 -> 变量申明。所以**函数声明会覆盖变量声明，但不会覆盖变量赋值**。

   ```js
   // 函数声明会覆盖变量声明
   function value() {
     return 1;
   }
   var value;
   console.log(typeof value); //"function"

   // 函数声明不会覆盖变量赋值
   function value() {
     return 1;
   }
   var value = 1;
   console.log(typeof value); //"number"
   ```

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

原型链实现对原型属性和方法的继承(需要共享的)，通过借用构造函数实现对实例属性的继承(不共享的)。即实现了函数复用，而且每个实例拥有自己的属性。

```js
// 父类的实例属性（不共享）
function Supertype(name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}

// 父类的原型属性和方法（共享）
Supertype.prototype.company = 'TD'; // 基本类型时各自查询各自的
Supertype.prototype.hobby = ['reading', 'shopping']; //为引用类型时共享（数组时）
Supertype.prototype.sayName = function() {
  console.log(this.name);
};

// 构造函数方式继承实例属性（不共享）
function Subtype(name, age) {
  Supertype.call(this, name);
  this.age = age;
}

// 原型链方式继承原型属性和方法（共享）
Subtype.prototype = new Supertype();
Subtype.prototype.constructor = Subtype;

Subtype.prototype.sayAge = function() {
  console.log(this.age);
};

// 实例
var super1 = new Supertype('Alex');
var super2 = new Supertype('Bob');
var sub1 = new Subtype('Candy', 20);
var sub2 = new Subtype('Doom', 30);

super1.sayName(); // Alex
super1.sayAge(); // not function
sub1.sayName(); // Candy
sub1.sayAge(); // 20

sub1.colors.push('black');
console.log(super1.colors); // ["red", "blue", "green"]，只影响sub1，其余3个不影响
delete sub1.colors;
console.log(sub1.colors); // 并不是 undefined，而是 ["red", "blue", "green"]
// 删除了从实例属性继承来的colors，读取colors会成为从原型继承来的实例属性

super1.hobby.push('chatting');
console.log(super2.hobby); // ["reading", "shopping", "chatting"]，四个都影响
super1.company = 'AM';
console.log(super2.company); // TD
```

- 优化组合继承：原型链继承时调用 2 次超类，修改为只调用一次的寄生式继承。
- 背后思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非是超类型原型的一个副本而已。
- 本质上：使用寄生式继承来继承超类型的原型，再将结果指定给子类型的原型。

```js
// 之前
Subtype.prototype = new Supertype();
Subtype.prototype.constructor = Subtype;

// 修改为
function inheritPrototype(Subtype, Supertype) {
  var prototype = Object.create(Supertype.prototype);
  prototype.constructor = Subtype;
  Subtype.prototype = prototype;
}
inheritProto(Subtype, Supertype);
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

- 常见误解三：当调用括号的左边不是引用类型而是其它类型，这个值自动设置为 null，结果为全局对象。

```js
var foo = {
  bar: function() {
    console.log(this);
  },
};

foo.bar(); // Reference, OK => foo
foo.bar(); // Reference, OK => foo

(foo.bar = foo.bar)(); // global? undefined
(false || foo.bar)(); // global? undefined
(foo.bar, foo.bar)(); // global? undefined
```

### new 的过程

```js
var newGen = function() {
  var obj = new Object(); // 从 Object.prototype 上克隆一个空的对象
  Constructor = [].shift.call(arguments); // 取得外部传入的构造器
  obj.__proto__ = Constructor.prototype; // 指向正确的原型
  var ret = Constructor.apply(obj, arguments); // 借用外部传入的构造器给 obj 设置属性
  return typeof ret === 'object' ? ret : obj; // 确保构造器总是会返回一个对象
};

function Person(name) {
  this.name = name;
}
var person1 = new Person('seven');
var person2 = newGen(Person, 'seven');
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
