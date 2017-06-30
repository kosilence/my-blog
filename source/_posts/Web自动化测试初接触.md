---
title: Web自动化测试初接触
date: 2017-02-08 17:03:40
desc: Web测试 自动化 单元测试 selenium
---

测试方向有很多

对于web项目来说，接触较多的大概为以下几类：

单元测试（unit testing）指的是以软件的单元（unit）为单位，对软件进行测试。单元可以是一个函数，也可以是一个模块或组件。

e2e，是end to end的缩写。e2e测试的方式是模拟用户行为，控制浏览器执行一系列的页面操作，例如点击按钮、输入文字等等，最后判断结果是否符合预期。

性能测试一般包括连接速度测试、压力测试等。

<!--more-->

-------

## 测试方向:

#### 1、单元测试

单元测试（unit testing）指的是以软件的单元（unit）为单位，对软件进行测试。单元可以是一个函数，也可以是一个模块或组件。它的基本特征就是，只要输入不变，必定返回同样的输出。多数用于逻辑运算函数输入输出的测试。
工程最佳实践为测试驱动＋测试框架＋(断言库)，参考资料[karma 测试框架的前世今生](http://taobaofed.org/blog/2016/01/08/karma-origin/);

[karma](https://github.com/karma-runner/karma)

测试驱动，是一个测试任务管理工具，可以很容易的和常见的各种测试框架结合（jasmine、mocha等）,可以根据不同环境，不同浏览器进行测试，也可以集成到CI环境，其重要特性就是可以监控文件的变化，然后自动执行测试，通过console.log观察测试结果

[Jasmine](https://jasmine.github.io/)

测试框架，集成度高，不依赖其他任何js组件（相对独立），有干净清晰的语法（即不加载额外的断言库），缺点在于异步控制的方法很长很麻烦（例如ajax请求），需要重新封装使用；

[mocha](https://github.com/mochajs/mocha)

测试框架，Mocha is a simple, flexible, fun JavaScript test framework for node.js and the browser.终端显示友好，扩展性好，自身不带断言库，需要搭配[chai](http://chaijs.com/)、[shouldjs](https://shouldjs.github.io/)等断言库使用

#### 2、e2e测试／功能测试

e2e，是end to end的缩写。e2e测试的方式是模拟用户行为，控制浏览器执行一系列的页面操作，例如点击按钮、输入文字等等，最后判断结果是否符合预期。
AngularJs提供了[Protractor](https://docs.angularjs.org/guide/e2e-testing)来做e2e测试，vue则需要搭一个测试方案，我们选择 Selenium/WebDriver + Nightwatch.js

[selenium中文文档](https://www.gitbook.com/book/wizardforcel/selenium-doc/details)

大名鼎鼎的selenium，一款用于web系统的自动测试工具，完成一系列浏览器的自动化操作。

[nightwatch](http://nightwatchjs.org/)

在Selenium/WebDriver的基础上，优化了测试用例的语法结构，可以方便的使用css选择器，支持CI；

[Selenium + Nightwatch搭建教程](https://segmentfault.com/a/1190000005991670)

简单的本地搭建教程

#### 3、性能测试

性能测试一般包括连接速度测试、压力测试等。
页面性能越来越受到关注，并且性能需要在开发过程中持续关注，否则很容易随着业务迭代而下降。
性能并不是一个目标，而是开发、测试过程中需要持续关注的问题。我们有自动化的工具和框架在开发时进行优化，同样可以借助工具在测试时进行性能测试。

[Phantomas](https://github.com/macbre/phantomas)

它能运行测试页面获取很多性能指标，加载时间、页面请求数、资源大小、是否开启缓存和Gzip、选择器性能、dom结构等等诸多指标都能一次性得到。

[google PageSpeed Tools](https://developers.google.com/speed/pagespeed/)

google页面性能测试工具，优化参考工具。

## 其他内容补充

#### 1、测试开发模式

TDD(Test Drivin Development)是测试驱动开发，强调的是一种开发方式，以测试来驱动整个项目，即先根据接口完成测试编写，然后在完成功能时要不断通过测试，最终目的是通过所有测试。

BDD(Behavior Drivin Development)行为驱动开发，可以理解为也是TDD的分支，即也是测试驱动，但BDD强调的是写测试的风格，即测试要写得像自然语言，运用一些比如expect、should等跟自然语言相近的断言，让项目的各个成员甚至产品都能看懂测试，甚至编写测试。

补充阅读：[关于TDD、BDD和DDD的一些看法](http://www.cnblogs.com/ustbwuyi/archive/2012/10/26/2741223.html)

#### 2、断言是什么

断言是用来判断实际值与预期值是否相等的工具。
断言有assert(node.js)、expect(expect.js)、should(should.js)三种风格，或者称为三种写法,[chai](http://chaijs.com/)支持上述三种写法

    // assert风格
    assert.equal(event.detail.item, '(item)‘);

    // expect风格
    expect(event.detail.item).to.equal('(item)');

    // should风格
    event.detail.item.should.equal('(item)');


