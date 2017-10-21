---
title: 前端知识体系梳理-ECMAScript
date: 2017-10-21 17:28:53
desc: 变量 作用域 内存 引用类型 面向对象 函数表达式
---

什么是执行环境和作用域？

构造函数模式和原型模式的完美结合？

继承为什么会有那么多的模式？

函数表达式的一些运用？

<!--more-->

## 变量、作用域和内存问题

#### 基本类型和引用类型的值

> ECMAScript 变量可能包含两种不同数据类型的值：基本类型值和引用类型值

1. 基本类型值是按值访问的，在内存中占据固定大小的空间，因此保存在栈内存中，可以直接被操作保存, `Undefined / Null / Boolean / Number / String`
2. 引用类型值是保存在堆内存中的对象，因为 JS 不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间，所以在操作对象时，实际上是在操作对象的引用，而不是实际的对象，为此，引用类型的值是按照引用访问的

#### 执行环境及作用域

执行环境是 JavaScript 中最为重要的一个概念。每个执行环境都有一个与之关联的变量对象，环境中定义的所有变量和函数都保存在这个对象中。

全局执行环境是最外层的一个执行环境，而每一个函数都有自己的执行环境，当执行流进入一个函数时，函数的环境就会被推入一个环境栈中，而在函数执行之后，栈将其环境弹出，把控制权交给之前的执行环境。

作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。

#### 垃圾收集

使用一旦数据不再有用，最好通过将其值设置为 `null` 来释放其引用 --- 这种做法叫做 **解除引用** 。

## 引用类型

> 常见类型：Object、Array、Date、RegExp、Function
>
> 基本包装类型：Boolean、Number、String
>
> 单体内置对象：Global、Math

## 面向对象的程序设计

#### 理解对象

**属性类型：**

1. 数据属性有4个描述特性： `Configurable(可否删除) / Enumerable(可否通过 for-in 返回) / Writable(可编辑) / Value(数据值)` ，要修改属性默认的特性，必须调用 `Object.defineProperty()` 方法。
2. 访问器属性不包含数据值，它们包含一对 `getter / setter` 函数： `Configurable / Enumeralbe / Get / Set` ，访问器属性不能直接定义，也需要通过 `Object.defineProperty()` 来定义。

#### 创建对象

**构造函数模式：**

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    alert(this.name);
  }
}
var person1 = new Person('Neo', 23, 'Doctor');
var person2 = new Person('Cosin', 43, 'Engineer');
```

通过 `new` 操作符来创建 `Person` 的新实例，实际上会经历一下四个阶段：

1. 创建一个新对象；
2. 将构造函数的作用域赋给新对象，因此 `this` 就指向了这个新对象；
3. 执行构造函数中的代码，为这个新对象添加属性；
4. 返回新对象。

构造函数很美好，有一个问题在于每一个实例化的 `Person` 都包含一个功能完全相同，而属于不同实例的 `sayName Function` ，这样的构造有些没有必要，所有为了优化它，我们引入 **原型模式** 。

**原型模式：**

`prototype` 就是通过调用构造函数而创建的那个对象的原型对象。其好处是可以让所有对象实例共享原型对象所包含的属性和方法。

```javascript
function Person() {
}
Person.prototype.name = 'Neo';
Person.prototype.age = 23;
Person.prototype.job = 'Doctor';
Person.prototype.sayName = function() {
  alert(this.name);
};

var person1 = new Person();
person1.sayName(); // 'Neo'
```

理解原型对象

![](https://ws1.sinaimg.cn/large/9eb6a82aly1fkoiateml6j20kv0bawew.jpg)

#### 组合使用构造函数模式和原型模式

构造函数模式用来定义实例属性，而原型模式用来定义方法和共享的属性。

每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度节省了内存，另外，这种混成模式还支持向构造函数传递参数。

#### 继承

> 常见的继承方式有两种：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。
>
> ECMAScript 只支持实现继承，而且其实现继承主要是依赖原型链来实现的。

**原型链**

基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。

![](https://ws1.sinaimg.cn/large/9eb6a82aly1fkpnuettdtj20oc0jkq59.jpg)

`SubType` 继承了 `SuperType` ，而 `SuperType` 继承了 `Object` 。当调用 `instance.toString()` 时，实际上调用的是保存在 `Object.prototype` 中的那个方法。

原型链的问题：

1. 在包含引用类型值的原型中，其原型属性会被所有实例共享；
2. 没有办法在不影响所有对象实例的情况下，给超类型（父类型）的构造函数传递参数。

**组合继承**

> 将原型链和借用构造函数的技术组合到一起，其思路是使用圆形脸实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。

```javascript
function superType(){
  this.name = name;
  this.colors = ["red","blue","green"];
}

superType.prototype.sayName = function(){
  alert(this.name);
};

function subType(name,age){
  //继承属性
  superType.call(this,name);
  this.age = age;
}
//继承方法
subType.prototype = new superType();
subType.prototype.constructor = subType(); //上一句导致重写了原型对象，所以要重新指定constructor的指向。
subType.prototype.sayAge = function(){
  alert(this.age);
}

var instance1 = new subType("Nico",20);
instance1.colors.push("black");
alert(instance1.colors);  //red,blue,green,black
instance1.sayName();  //"Nico"
instance1.sayAge();   //20 

var instance2 = new subType("Greg",28);
instance1.colors.push("black");
alert(instance1.colors);  //red,blue,green
instance1.sayName();  //"Greg"
instance1.sayAge();   //28
```

组合继承的问题：无论什么情况下，都会调用两次超类型（父）构造函数，一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。

**寄生组合式继承**

> 通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。
>
> 其思路是：不必为了指定子类型的原型而调用超类型（父）的构造函数，我们所需要的无非就是超类型原型的一个副本而已。

```javascript
function inheritPrototype(subType, superType) {
  var prototype = Object(superType.prototype);	// 创建对象
  prototype.constructor = subType;				// 增强对象
  subType.prototype = prototype;				// 指定对象
}
```

寄生组合式继承，给予某个对象或者某些信息创建一个对象，然后增强对象，最终返回对象。将最终获取到的对象结果指定给子类型的原型。

## 函数表达式

> 定义函数的方法有两种：一种是函数声明，另一种就是函数表达式。

```javascript
// 函数声明，其重要特征是函数声明提升，意思是执行代码之前就会先读取函数声明
function functionName(num1, num2) {
}
// 函数表达式，如下是一种常见的形式，创建一个匿名函数并赋值给一个变量
var functionName = function(num1, num2) {
}
```

#### 递归

通过使用 `arguments.callee` 代替函数名，可以确保无论怎么调用函数都不会出问题

```javascript
function factorial(num) {
  if(num <= 1) {
    return 1;
  }else {
    // arguments.callee 是一个指向正在执行的函数的指针
    return num * arguments.callee(num - 1);
  }
}
// 严格模式下，不能通过脚本访问 arguments.callee，可以使用命名函数表达式来完成
var factorial = (function f(num) {
  if(num <= 1) {
    return 1;
  }else {
    return num * f(num - 1);
  }
});
```

#### 闭包

> 闭包是指有权访问另一个函数作用域中的变量的函数。

当某个函数被调用时，会创建一个执行环境以及相应的作用域链。然后使用 `arguments` 和其他命名参数的值来初始化函数的活动对象。

匿名函数的执行环境具有全局性，因此其 `this` 对象通常指向 `window` 。

```javascript
var name = "the window";
var object = {
  name: "the object",
  getName: function() {
    return function() {
      return this.name;
    };
  }
};

alert(object.getName()()); // "the window"
```

如果想访问作用域中的 `this / arguments` 对象，必须将对该对象的引用保存到另一个闭包能够访问的变量中。

#### 模仿块级作用域

ES5 中还没有块级作用域的概念，我们可以通过匿名函数来模仿块级作用域。

```javascript
function outpuitNum(count) {
  (function() {
   	// 块级作用域
    for(var i=0; i<count; i++) {
      alert(i);
    }
  })();
  
  alert(i); // 导致错误，i 未定义
}
```

这种做法可以减少闭包占用的内存问题，减少全局变量冲突。

#### 私有变量

在函数的外部无法访问其内部的变量，如果在这个函数内部创建一个闭包，那么闭包通过自己的作用域链可以访问到这些变量，利用这一点，我们可以创建用于访问私有变量的公有方法，称之为特权方法。

