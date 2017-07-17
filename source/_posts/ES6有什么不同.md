---
title: 'ES6 有什么不同'
date: 2017-07-14 10:30:00
desc: 
---

ES6 的第一个版本在2015年6月发布，正式名称就是《ECMAScript 2015标准》（简称 ES2015）

ES6 既是一个历史名词，也是一个泛指，含义是5.1版以后的 JavaScript 的下一代标准，涵盖了ES2015、ES2016、ES2017等等，而ES2015 则是正式名称，特指该年发布的正式版本的语言标准。

ES6，一般是指 ES2015 标准，但有时也是泛指“下一代 JavaScript 语言”。

<!--more-->

-------

> 文章源自[阮一峰-ECMAScript 6 入门](http://es6.ruanyifeng.com)，本文记录了学习过程中需要多加注意和理解的部分。

## let 和 const 命令

#### let 命令

**1.** 用来声明变量

用法类似于 `var`，但是所声明的变量，只在 `let` 命令所在的代码块内有效。

```
# 常见用法
for(let i = 0; i < 10; i++) {
    // dosometing
}
```

**2.** 暂时性死区

只要一进入当前作用域，索要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

ES6 规定，如果区块中存在 `let` 和 `const` 命令，这个区块对这些命令声明的额变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

**3.** 不允许重复声明

`let` 不允许在相同作用域内，重复声明同一个变量。

```
// error
function() {
    let a = 10;
    var a = 1;
}
```

#### 块级作用域

ES5 只有全局作用域和函数作用域，没有块级作用域，这并不合理。

```
// 内层变量可能会覆盖外层变量
var tmp = new Date();

function f() {
    console.log(tmp);
    if(false) {
        var tmp = "hello world";
    }
}
```

#### const 命令

`const` 声明一个只读的常量。一旦声明，常量的值就不能改变。

```
const PI = 3.14159;

console.log(PI);
// 3.14159

PI = 3;
// TypeError: Assignment to constant variable.
```

`const` 的作用域与 `let` 命令相同：只在声明所在的块级作用域内有效。并且不可重复声明。

实际上，`const` 保证的是变量指向的内存地址不变动，对于复合类型（对象和数组）的数据，需要非常小心。

#### 顶层对象属性

为了保持兼容性，`var` 和 `function` 命令声明的全局变量，依旧是顶层对象的属性。

另一方面，`let` 、 `const` 、`class` 命令声明的全局变量，不属于顶层对象的属性。

```
var a = 1;
window.a // 1

let b = 1;
window.b // undefined
```

## 变量的解构赋值

ES6 允许按照一定模式，从书组合独享中提取之，对变量进行赋值，这被称为解构。

本质上，这种写法属于*模式匹配*，只要等号两边的模式相同，左边的变量就会被赋予对应的值。

```
// 数组
let [a, b, c] = [1, 2, 3];

// 默认值
let [foo = true] = [];
foo // true

// 对象
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

// 字符串
let [a, b, c, d, e] = "hello";
a // "h"
e // "o"
```

#### 用途

```
// 交换变量的值
let x = 1;
let y = 2;

[x, y] = [y, x];

// 从函数返回多个值
function example() {
    return [1, 2, 3];
}
let [a, b, c] = example();

// 函数参数的定义
// 参数是一组无次序的值
function f({x, y, z}) {....}
f({z: 3, x: 2, y: 1});

// 提取JSON数据
let jsonData = {
    id: 32,
    status: "OK",
    data: [222, 333]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);

// 遍历Map解构
var map = new Map();
map.set("first", "hello");
map.set("second", "world");

for(let [key, value] of map) {
    console.log(key + "is" + value);
}
// first is hello
// second is world

// 输入模块的指定方法
const { SourceMap, SourceNode } = require("source-map");
```

## 字符串、正则、数值、函数、数组、对象的扩展

#### 字符串

ES6 加强了对 Unicode 的支持，并扩展了字符串对象，详情见[字符串的扩展](http://es6.ruanyifeng.com/#docs/string)

**字符串的遍历器接口**

```
for (let codePoint of 'foo') {
    console.log(codePoint)
}
// "f"
// "o"
// "o"
```

**includes(), startsWith(), endsWith()**

```
var s = 'Hello world!';
s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

**repeat()**

```
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

**模板字符串**

```
// 传统的写法
$('#result').append(
    'There are <b>' + basket.count + '</b> ' +
    'items in your basket, ' +
    '<em>' + basket.onSale +
    '</em> are on sale!'
);

// 模板字符串，使用 `` 反引号标识，嵌入的变量写在 ${} 之中
$('#result').append(`
    There are <b>${basket.count}</b> items
    in your basket, <em>${basket.onSale}</em>
    are on sale!
`);
```

#### 数字

**Number.isFinite(), Number.isNaN()**

`Number.isFinite()` 用来检查一个数值是否为有限的（finite）

`Number.isNaN()` 用来检查一个值是否为 `NaN`

**Number.parseInt(), Number.parseFloat()**

ES6 将全局方法 `parseInt()` 和 `parseFloat()`，移植到 `Number` 对象上面，行为完全保持不变。这样做是为了减少全局性方法，使语言逐步模块化。

```
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6的写法
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```

**Math对象的扩展**

新增了17个与数学有关的方法，都是静态方法，只能在 `Math` 对象上调用，详见[Math对象的扩展](http://es6.ruanyifeng.com/#docs/number#Math对象的扩展)

#### 函数

**函数参数的默认值**

```
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

**严格模式**

从 ES5 开始，函数内部可以设定为严格模式。

ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。

```
function doSomething(a, b) {
    'use strict';
    // code
}

// 报错
function doSomething(a, b = a) {
    'use strict';
    // code
}
```

函数执行的时候，先执行函数参数，然后再执行函数体。

这样就有一个不合理的地方，只有从函数体之中，才能知道参数是否应该以严格模式执行，但是参数却应该先于函数体执行。

两种方法可以规避这种限制。第一种是设定全局性的严格模式，这是合法的。

```
'use strict';
function doSomething(a, b = a) {
    // code
}
```

第二种是把函数包在一个无参数的立即执行函数里面。

```
const doSomething = (function () {
    'use strict';
    return function(value = 42) {
        return value;
    };
}());
```

**箭头函数**

```
// 正常函数写法
var result = values.sort(function (a, b) {
  return a - b;
});

// 箭头函数写法
var result = values.sort((a, b) => a - b);
```

## Promise 对象

`Promise` 对象有两个特点：

1. 对象的状态不受外界影响。`Promise` 对象代表一个异步操作，有三种状态：`Pending`（进行中）、`Resolved`（已完成，又称 Fulfilled）和`Rejected`（已失败）。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。

**基本用法**

```
var promise = new Promise(function(resolve, reject) {
    // ... some code
    
    if (/* 异步操作成功 */){
        resolve(value);
    } else {
        reject(error);
    }
});

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```

## Class 类

JavaScript 语言中，生成实例对象的传统方法是通过构造函数。

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过`class`关键字，可以定义类。

ES6 的`class`可以看做只是一个语法糖，新的`class`写法只是让对象原型的写法更加清晰，更像面向对象编程的语法而已。

#### 基本用法

```
// 构造函数
function Point(x, y) {
    this.x = x;
    this.y = y;
}

Point.prototype.toString = function () {
    return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);

// 定义类
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
    
    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}
```

#### Class 的继承

Class 可以通过extends关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

```
class Point {
}

class ColorPoint extends Point {
}
```

#### super 关键字

`super` 这个关键字，既可以当作函数使用，也可以当作对象使用。

第一种情况，`super` 作为函数调用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次 `super` 函数。

```
class A {}

class B extends A {
    constructor() {
        // 代表调用父类的构造函数
        super();
    }
}
```

第二种情况，`super` 作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

```
class A {
    p() {
        return 2;
    }
}

class B extends A {
    constructor() {
        super();
        console.log(super.p()); // 2
    }
}

let b = new B();
```

#### 区分 ES5 和 ES6 中的继承概念：prototype、constructor、__proto__

> 有图更好理解，ES5中这种最简单的继承，实质上就是将子类的原型设置为父类的实例。

![](http://ww1.sinaimg.cn/large/9eb6a82aly1fhmp8zlk9qj20el0ep75u.jpg)

```
function Super() {}
 
function Sub() {}
Sub.prototype = new Super();
Sub.prototype.constructor = Sub;
 
var sub = new Sub();
 
Sub.prototype.constructor === Sub; // ② true
sub.constructor === Sub; // ④ true
sub.__proto__ === Sub.prototype; // ⑤ true
Sub.prototype.__proto__ == Super.prototype; // ⑦ true
```

![](http://ww1.sinaimg.cn/large/9eb6a82aly1fhmpmt8mtej20e00e8wg1.jpg)

```
class Super {}
 
class Sub extends Super {}
 
var sub = new Sub();
 
Sub.prototype.constructor === Sub; // ② true
sub.constructor === Sub; // ④ true
sub.__proto__ === Sub.prototype; // ⑤ true
Sub.__proto__ === Super; // ⑥ true
Sub.prototype.__proto__ === Super.prototype; // ⑦ true
```

## Module 语法

ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

`CommonJS` 和 `AMD` 模块，都只能在运行时确定这些东西。比如，`CommonJS` 模块就是对象，输入时必须查找对象属性。

```
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// ES6模块
import { stat, exists, readFile } from 'fs';
```

上面 ES6 代码的实质是从`fs`模块加载3个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

#### export 命令

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。

```
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;
function getData() {}

// 输出变量和函数
export { firstName, lastName, year, getData };
```

#### import 命令

使用 `export` 命令定义了模块的对外接口以后，其他 JS 文件就可以通过 `import` 命令加载这个模块。

```
// main.js
import {firstName, lastName, year} from './profile';

function setName(element) {
    element.textContent = firstName + ' ' + lastName;
}
```

#### 模块的整体加载

除了指定加载某个输出值，还可以使用整体加载，即用星号 `*` 指定一个对象，所有输出值都加载在这个对象上面。

```
import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```

#### export default 命令

```
// import-default.js
import customName from './export-default';
customName(); // 'foo'

// 等同于
import { default as customName } from './export-default';
```

上面代码的import命令，可以用任意名称指向export-default.js输出的方法，这时就不需要知道原模块输出的函数名。需要注意的是，这时import命令后面，不使用大括号。

## Module 的加载实现

#### 传统方法

在 HTML 网页中，浏览器通过`<script>`标签加载 JavaScript 脚本。

```
// 默认标签，停下来执行脚本，然后继续向下渲染
<script src="path/to/myModule.js"></script>

// 两种异步加载的语法
<script src="path/to/myModule.js" defer></script>
<script src="path/to/myModule.js" async></script>
```

`defer` 与 `async` 的区别是：前者要等到整个页面正常渲染结束，才会执行；后者一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。

一句话，`defer` 是“渲染完再执行”，`async` 是“下载完就执行”。另外，如果有多个 `defer` 脚本，会按照它们在页面出现的顺序加载，而多个 `async` 脚本是不能保证加载顺序的。

#### module 加载规则

```
<script type="module" src="foo.js"></script>
```

浏览器对于带有 `type="module"` 的 `<script>`，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了 `<script>` 标签的 `defer` 属性。

#### ES6 模块与 CommonJS 模块的差异

有两个重大差异：
**1.** CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
**2.** CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

```
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};

// main.js
var mod = require('./lib');

console.log(mod.counter);  // 3
mod.incCounter();
console.log(mod.counter); // 3
```

CommonJS 一旦输出一个值，模块内部的变化就影响不到这个值，这是因为 `mod.counter` 是一个原始类型的值，会被缓存。除非写成一个函数，才能得到内部变动后的值。

ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。

ES6 模块之中，顶层的 `this` 指向 `undefined`；CommonJS 模块的顶层 `this` 指向当前模块，这是两者的一个重大差异。

## 编程风格

#### 块级作用域

**1. `let` 取代 `var`**

**2. 全局常量和线程安全**

在 `let` 和 `const` 之间，建议优先使用 `const`，尤其是在全局环境，不应该设置变量，只应设置常量。

#### 字符串

静态字符串一律使用单引号或反引号，不使用双引号。动态字符串使用反引号。

#### 解构赋值

使用数组成员对变量赋值时，优先使用解构赋值。

```
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```

#### 数组

使用扩展运算符 `...` 拷贝数组。

```
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

#### Class

总是用 `Class`，取代需要 `prototype` 的操作。因为 `Class` 的写法更简洁，更易于理解。

```
// bad
function Queue(contents = []) {
    this._queue = [...contents];
}
Queue.prototype.pop = function() {
    const value = this._queue[0];
    this._queue.splice(0, 1);
    return value;
}

// good
class Queue {
    constructor(contents = []) {
        this._queue = [...contents];
    }
    pop() {
        const value = this._queue[0];
        this._queue.splice(0, 1);
        return value;
    }
}
```

使用 `extends` 实现继承，因为这样更简单，不会有破坏 `instanceof` 运算的危险。

```
// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
    Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
    PeekableQueue.prototype.peek = function() {
    return this._queue[0];
}

// good
class PeekableQueue extends Queue {
    peek() {
        return this._queue[0];
    }
}
```

#### 模块

首先，Module 语法是 JavaScript 模块的标准写法，坚持使用这种写法。使用 `import` 取代 `require`。

```
// bad
const moduleA = require('moduleA');
const func1 = moduleA.func1;
const func2 = moduleA.func2;

// good
import { func1, func2 } from 'moduleA';
```

使用 `export` 取代 `module.exports`。

```
// commonJS的写法
var React = require('react');

var Breadcrumbs = React.createClass({
    render() {
        return <nav />;
    }
});

module.exports = Breadcrumbs;

// ES6的写法
import React from 'react';

class Breadcrumbs extends React.Component {
    render() {
        return <nav />;
    }
};

export default Breadcrumbs;
```

