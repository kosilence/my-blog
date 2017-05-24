---
title: 读书笔记：JavaScript 设计模式
date: 2017-05-06 14:20:41
---

《Learning JavaScript Design Patterns》

设计模式是解决软件设计中常见问题的可复用方案。

探索任何编程语言时，设计模式都是一个令人兴奋和极具吸引力的话题

<!--more-->

## 第一章 介绍

## 第二章 什么是模式

## 第三章 模式状态测试、Proto 模式及三法则

#### 1、proto-pattern 原始模式

#### 2、三法则

适合性、实用性、适用性。

## 第四章 设计模式的结构

## 第五章 编写设计模式

## 第六章 反模式

大概意思就是一种值得记录的不良设计

## 第七章 设计模式类别

#### 1、创建型设计模式

比如：*Constructor-构造器、Factory-工厂、Abstract-抽象、Prototype-原型、Singleton-单例、Builder-生成器*

#### 2、结构型设计模式

比如：*Decorator-装饰者、Facade-外观、Flyweight-享元、Adapter-适配器、Proxy-代理*

#### 3、行为设计模式

比如：*Iterator-迭代器、Mediator-中介者、Observer-观察者、Visitor-访问者*

## 第八章 设计模式分类

提到了**23种**设计模式

## 第九章 JavaScript 设计模式

#### 1、Constructor-构造器 模式

内存已经分配给该对象的情况下，用于初始化新创建对象的特殊方法。

**一个基本的构造器模型**

```
function Car(model, year, miles) {
	this.model = model;
	this.year = year;
	this.miles = miles;

	this.toString = function() {
		return this.model + "has done " + this.miles + 'miles';
	};
}

var car1 = new Car("Honda Civic", 2009, 20000);
var car2 = new Car("Ford Mondeo", 2010, 5000);

// 使用对象内的函数输出
console.log(car1.toString());
console.log(car2.toString());
```

**带原型的构造器模型**

使用 JavaScript 中的 *prototype* 属性，从而使多个 *Car* 对象可以访问相同的原型

```
function Car(model, year, miles) {
    this.model = model;
    this.year = year;
    this.miles = miles;
}

Car.prototype.toString = function() {
    return this.model + "has done " + this.miles + 'miles';
};

// 用法
var car1 = new Car("Honda Civic", 2009, 20000);
var car2 = new Car("Ford Mondeo", 2010, 5000);

console.log(car1.toString());
console.log(car2.toString());
```

现在 *toString()* 方法可以在所有 *Car* 对象中共享，好玩的方法

#### 2、Module-模块 模式

模块可以帮助我们清晰地分离和组织项目中的代码单元

**对象字面量**

```
var myModule = {
    // 对象字面量可以包含属性和方法

    // 普通属性
    myName: "hello-kitty",

    // 基本方法
    myMethod: function() {
        console.log('My name is ' + this.myName);
    },
    
    // 对象属性
    myObject: {
        age: 18,
        money: 0
    }
};
```

**私有和公有封装方法**

JavaScript 中，Module 模式进一步的模拟了类的概念，通过这种方法，使得一个单独的对象可以拥有公有/私有方法和变量，从而屏蔽其他全局作用域的函数变量带来的不稳定因素。

产生的结果是：大大降低了函数名与页面中其他脚本定义的函数冲突的可能！

Module 模式内，由于闭包的存在，*哈哈，闭包！你咋起了个这名，难听又难懂*，声明的变量和方法只在该模式内部可用。但是，如果对象返回了变量或者方法，则这些变量或者方法对于外部访问者就是可用的了。

**实现一个简单的 Module 模式**

```
var testModule = (function() {

    // 定义一个私有变量
    var counter = 0;
    // 定义一个私有函数
    function doSometingPrivate() {
        // ...
    }
    
    // 返回一个暴露出的公有对象，包含了外部可用的变量或者函数
    return {
        // 公有变量
        productName: 'adidas', 
        // 调用了私有变量的公有函数
        addCounter: function() {
            return ++counter;
        },  
        resetCounter: function() {
            counter = 0;
        }
    };
})();
```

有种类似于`php`中`namespace`的感觉

**优点和缺点**

这种类似于封装的思想，更加简洁，而且使代码拥有了私有数据空间。

但是在我们想要改变成员的可见性时，需要修改每一个曾经使用过该成员的地方。

#### 3、Revealing Module-揭示模块 模式

**为什么会创造这么一个和`Module-模块模式`很相似的模式？**

Module-模块 模式

1. 想要从另一个方法调用一个公有方法或者访问公有变量时，必须重复主对象的名称；
2. 主对象必须通过对象字面量表示法来定义成员

Revealing Module-揭示模块 模式

1. 能够在私有范围内简单定义所有的函数和变量，并返回一个匿名对象
2. 暴露的公有函数成员中，拥有指向私有函数的指针

**实现一个Revealing Module 模式**

```
var myRevealingModule = function() {
    
    var privateValue = "http";
    publicValue = "https";
    
    function privateFunction() {
        console.log(privateValue);
    }
    
    function publicSetName(strName) {
        privateName = strName;
    }
    
    function publicGetName() {
        privateFunction();
    }
    
    return {
        setName: publicSetName,
        value: publicValue,
        getName: publicGetName
    };
}();
```

**优点和缺点**

这种模式与脚本语法更一致，而且，模块代码底部会明确写出暴露出的公开函数和变量，改善可读性。

私有函数引用公有函数时，在需要打补丁时，公有函数是不能被覆盖的。（没懂这是什么意思，先记录一下）

#### 4、Singleton-单例 模式

这种模式限制了类只能被实例化一次

如果该实例不存在，可以通过一个方法创建一个类来实现创建类的新实例；

如果该实例已经存在，则会简单地返回该对象的引用。

**没懂**

## 第十章 JavaScript MV* 模式

#### 1、MVC 模型-视图-控制器

*Model-模型* 管理应用程序的数据，不涉及用户界面和表现层，代表业务相关的数据。

*View-视图*  是应用程序数据的可视化表示，表示当前状态的筛选视图。

*Controller-控制器* 是中介，当用户操作 *View-视图* 时，他负责更新 *Model-模型* 。然而在 JavaScript 框架中，控制器这个角色并不是很明显，没有形成明确的概念。它的大部分功能已经被 *View-视图* 和 *Route-路由* 配合完成了。

**MVC 为我们提供了什么**

* 整体维护更容易。明确了视图和数据的关系。
* 解耦 *Model-模型* 和 *View-视图*。可以更直接的编写业务逻辑的单元测试。
* 消除底层 *Model-模型* 和 *Controller-控制器* 代码的重复。
* 可以分离核心逻辑和用户界面的开发工作。

**MVC** 依赖于 **Observer-观察者 模式** 来实现它的核心通信，当 *Model-模型* 被改变时，它通知其观察者 *View-视图* 们一些内容已经更新，这种关系的观察者本质上也是促进多个 *View-视图* 被附加到同一个 *Model-模型* 中的因素。这个也许是 **MVC** 中最重要的关系。

#### 2、MVP 模型-视图-表示器

与 **MVC** 不同的是，*View-视图* 把调用委托给 *Presenter-表示器*，*Presenter-表示器* 是从 
*View-视图* 中解构，并且通过接口和它对话的。

这种变化能沟通有效提高应用程序的可测试性，并在 *View-视图* 和 *Model-模型* 之间提供更清晰的分离。这种分离缺乏数据绑定支持，因此需要付出更多的关注。

**不要强迫某个框架完全适用于某个特定的设计模式。**设计模式应该被看作是构建应用程序的灵活指南，重要的是可以帮助我们开发出有组织性、整洁并易于维护的应用程序。

#### 3、MVVM 模型-视图-视图模型

**MVVM** 试图更清晰的将用户界面从应用程序的业务逻辑与行为中分离。

*Model-模型* 中保存着信息，通常不会处理行为。通常会使用 Ajax 调用服务器端的服务来进行读写操作。

*View-视图*  是一个交互式 UI，描述 *ViewModel-视图模型* 的状态，它并不负责处理状态。仅仅是保持状态同步的作用。

*ViewModel-视图模型* 可以当做一个专门的 *Controller-控制器*，充当数据转换器。它位于 UI 层后面，充当*View-视图* 和 *Model-模型* 之间的中间人。

**View 和 ViewModel** 之间通过数据绑定和事件进行通信。

**MVVM 的优点**

* 使得 UI 和 行为逻辑 并发开发更加容易
* View 抽象化，减少代码背后所需的业务逻辑量
* ViewModel 更好的单元测试体验

**MVVM 的缺点**

* 简单的 UI 环境下，并不需要 MVVM 来实现
* 数据绑定是声明式的话，更难调试
* 数据绑定产生大量的标记

#### 4、MVC、MVP 和 MVVM

MVC 与其衍生品之间的主要区别是每一层对其它层的依赖，以及他们是怎样紧密地互相绑定的。

在 **MVC** 中，View 了解 Controller，Controller 了解 Model，View 可以直接访问 Model，但是，向 View 暴露完整的 Model 可能带来安全性和性能成本。MVVM 试图避免这些问题。

在 **MVP** 中，Controller 的作用被 Presenter 所替代，Presenter 与 View 位于同一位置，监听 View 和 Model 的事件，调解他们的行动。与 MVVM 不同，它并没有数据绑定机制，因此需要依赖每个 View 实现两者通信的接口。

## 第十一章 模块化的 JavaScript 设计模式

#### 1、AMD 异步模块定义

* 更灵活的完成模块定义。
* 使用简洁的方式声明可能的独立模块和依赖，比全局变量或者 `<script>` 标签更加简洁。
* 封装模块定义，避免全局命名空间被污染。
* 没有跨域、本地或调试的问题，也不依赖服务器端工具。

一个小事例

```
var jQuery = this.jQuery || "jQuery";
var $ = this.$ || "$";
var originaljQuery = jQuery;
var original$ = $;

define(["jQuery"], function($) {
    $(".items").css("color", "green");
    return function() { };
});
```

#### 2、CommonJS 

CommonJS 模块基本上包含两个部分：

**自由变量 exports**，它包含了一个模块希望其他模块能够使用的对象。

**require 函数**，模块可以使用该函数导入`import`其他模块的导出`exports`。

**对比 AMD 和 CommonJS**

```
// CommonJS
var lib = require("package/lib");
function foo() {
    lib.dosomething();
}
// 导出（暴露）foo 给其他模块
exports.foo = foo;
```

```
// AMD
define(function(require) {
    var lib = require("package/lib");
    function foo() {
        lib.dosomething();
    }
    // 导出（暴露）foo 给其他模块
    return {
        foobar: foo
    }
});
```

