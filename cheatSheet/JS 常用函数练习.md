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
