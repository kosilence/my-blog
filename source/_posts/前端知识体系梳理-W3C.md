---
title: 前端知识体系梳理-W3C
date: 2017-10-08 10:30:05
desc: W3C HTML CSS JavaScript
---

`HTML / HTML5`、 `CSS / CSS3`、 `JavaScript` 查漏补缺

<!--more-->

## HTML / HTML5

#### 标签元素

类型包括：行内元素、块级元素、空 (void) 元素

#### 语义化

> 用正确的标签做正确的事情。

1. 让页面的内容结构化，便于让浏览器和搜索引擎解析；
2. 在没有 CSS 的情况下，使网页以一种文档格式显示，容易阅读；
3. 搜索引擎的爬虫依赖标记来确定上下文和各关键字的权重，利于 SEO；
4. 使阅读源代码的人更容易对网站分块，便于阅读维护理解。

#### DOM

> Document Object Model

根据 W3C 的 HTML DOM 标准，HTML 文档中的所有内容都是节点。节点的关系包括：父、子、兄弟节点。

#### HTML5

> HTML5 是对 HTML 标准的第五次修订。其主要的目标是将互联网语义化，以便更好地被人类和机器阅读，并同时提供更好地支持各种媒体的嵌入。

1. DOCTYPE 文档类型标记的目的是要告诉解析器用什么样的方式来解析文档。HTML5 提供的 `<!DOCTYPE html>` 是标准模式的解析方式，向后兼容，浏览器按照 W3C 的标准来解析渲染页面。
2. HTML5 提供了一些新的元素和属性，反映典型的现代用法网站。其中有些是技术上类似 `<div>` 和 `<span>` 标签，但有一定含义，例如 `<nav>` （网站导航块）和 `<footer>` `<audio>` 和 `<video>` 标记。
3. 脚本和链接无需 `type` ，标签引入 `CSS` 和 `JavaScript` 时，不需要再指定类型属性。
4. 多媒体，用于媒介回放的 `video` 和 `audio` 元素。
5. 图像效果，新增用于绘画的 `canvas` 和 `svg` 元素。
6. 离线存储，对本地离线存储有更好的支持，`localStore` 和 `Cookies` 等。
7. 设备兼容特性 ，HTML5 提供了前所未有的数据与应用接入开放接口。使外部应用可以直接与浏览器内部的数据直接相连，例如用户定位信息等。
8. 连接特性，更有效的连接工作效率，使得基于页面的实时聊天，更快速的网页游戏体验，更优化的在线交流得到了实现。HTML5 拥有更有效的服务器推送技术，`Server-Sent Event` 和 `WebSockets` 就是其中的两个特性，这两个特性能够帮助我们实现服务器将数据“推送”到客户端的功能。

## CSS / CSS3

#### 盒模型

CSS3 新增了一种盒模型计算方式：`box-sizing` 。

此属性的默认值为 `content-box` ：

`Width = width + padding-left + padding-right + border-left + border-right`

`Height = height + padding-top + padding-bottom + border-top + border-bottom`

新增的值 `padding-box` ：

`Width = width(包含padding-left + padding-right) + border-top + border-bottom`

`Height = height(包含padding-top + padding-bottom) + border-top + border-bottom`

 以及 `border-box` ：

`Width = width(包含padding-left + padding-right + border-left + border-right)`

`Height = height(包含padding-top + padding-bottom + border-top + border-bottom)`

#### 层叠规则

1. **用户端样式表** ：来自浏览器的样式，被称作 `UA style` ；
2. **用户样式表** ：这个样式表是使用浏览器的用户，根据自己的偏好设置的样式表，被称作 `user declarations`；
3. **作者样式表** ：即开发者在开发网页时，所定义的样式表，被称作 `author declarations`；

CSS 层叠顺序 ：

1. 对于目标媒介类型 (media type)，需要找到存有疑问的元素和属性的所有声明来进行比对。
2. 根据 CSS 样式的来源和重要性(是否含 !important )，给出了优先级的排列顺序：`user important declarations > author important declarations > author normal declarations > user normal declarations > UA declarations`
3. 拥有相同重要性和来源的规则，按照 CSS specificity 来排序。注意，选择器的特殊性是在相同来源，相同重要性的规则之间判定最终哪个规则会起作用。
4. 最后，根据先后次序来排列：如果两条规则具有相同的权重，相同的来源和相同的选择器特殊性，则后出现的规则超越先出现的规则。引入的样式表 `@import` 中的规则被认为出现在样式表本身的所有规则之前。

#### 选择器

选择器类型：

1. 基本选择器：通配符选择器、id 选择器、类选择器、元素选择器、父子兄弟选择器等；
2. 属性选择器：CSS3 中，指定元素的某个属性，或者同时指定属性以及其属性值；
3. 伪类选择器：比如 `:hover / :link / :nth` 等；

`CSS` 选择器的权重顺序：`important > 内联 > ID > 类 | 伪类 | 属性选择 > 标签 > 伪对象 > 继承 > 通配符`

特殊性的计算规则 ：

> 特殊性的值可以看作是一个由四个数组成的一个组合，用 a，b，c，d 来表示它的四个位置。 依次比较 a，b，c，d 这个四个数比较其特殊性的大小。比如，a 值相同，那么 b 值大的组合特殊性会较大，以此类推。

a，b，c，d 值的确定规则：

1. 如果 HTML 标签的 'style' 属性中该样式存在，则记 a 为 1；
2. 数一下选择器中 ID 选择器的个数作为 b 的值；
3. 其他属性、类以及伪类（`pseudo-classes`）的总数量是 c 的值；
4. 元素名和伪元素的数量是 d 的值。

W3C 官方给出的例子：

```
*             {}  /* a=0 b=0 c=0 d=0 -> specificity = 0,0,0,0 */
li            {}  /* a=0 b=0 c=0 d=1 -> specificity = 0,0,0,1 */
li:first-line {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
ul li         {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
ul ol+li      {}  /* a=0 b=0 c=0 d=3 -> specificity = 0,0,0,3 */
h1 + *[rel=up]{}  /* a=0 b=0 c=1 d=1 -> specificity = 0,0,1,1 */
ul ol li.red  {}  /* a=0 b=0 c=1 d=3 -> specificity = 0,0,1,3 */
li.red.level  {}  /* a=0 b=0 c=2 d=1 -> specificity = 0,0,2,1 */
#x34y         {}  /* a=0 b=1 c=0 d=0 -> specificity = 0,1,0,0 */
style=""          /* a=1 b=0 c=0 d=0 -> specificity = 1,0,0,0 */
------------------------------------------------------------------------
<HEAD>
	<STYLE type="text/css">
 		#x97z { color: red }
	</STYLE>
</HEAD>
<BODY>
	<P ID=x97z style="color: green">
</BODY>
```



#### 样式排版

`display` 属性： `block / inline / inline-block / table-cell`；

`position` 属性： `static / relative / absolute / fixed` ；

`float` 属性： `none / left / right / inherit` ；

[如何只用CSS做到完全居中](http://blog.jobbole.com/46574/)

#### 动画

CSS3 动画主要包括三种：

1. `transform` 变形：旋转、缩放、位移、斜切；
2. `transition` 过渡：会有一个形变的过程，会有过渡和形变时间；
3. `animation` 动画：设置动画的播放时间、方式、次数、方向等等。

## JavaScript

#### DOM

> DOM 是针对 HTML 和 XML 文档的一个 API

节点层次：常见的有  `Node / Document / Element / Text / Comment ` 类型；

DOM Selectors API ： `querySelector()` 、 `querySelectorAll()`；

#### BOM

> Browser Object Model

window 对象 ： 它表示浏览器的一个实例。它既是通过 JS 访问浏览器窗口的一个接口，又是  ECMAScript 规定的 `Global` 对象。

location 对象 ：

1. `window.location === document.location`
2. 位置操作： `location.href / hash / search / hostname / pathname / port`

navigator 对象 ： 其属性通常用于检测显示网页的浏览器类型。

screen 对象 ： 包含浏览器窗口外部的显示器信息等。

history 对象 ： 提供访问浏览器的历史记录的数量，以及向后或者向前导航到任意页面。

#### 事件

事件流：

1. 事件冒泡：事件开始时，由最具体的元素接收，然后逐级向上传播最终到 window 对象。
2. 事件捕获：与事件冒泡的传递顺序相反。
3. DOM 事件流：一共三个阶段，事件捕获阶段、处于目标阶段、事件冒泡阶段。一般建议将事件处理程序添加哀悼事件流的冒泡阶段，可以最大兼容。

事件对象：

1. 常用方法： `event.preventDefault()` 、 `event.stopPropagation()`
2. IE  中 `event` 需从 `window.event` 获取

```javascript
var EventUtil = {
	// 兼容 IE 的方法
	getEvent: function(event) {
    	return event ? event : window.event;
	},
	getTarget: function(event) {
    	return event.target || event.srcElement;
	},
	preventDefault: function(event) {
    	if(event.preventDefault) {
			event.preventDefault();
		}else {
        	event.returnValue = false;
		}
	},
	stopPropagation: function(event) {
		if(event.stopPropagation) {
        	event.stopPropagation();
		}else {
          	event.cancelBubble = true;
		}
	}
}
```

#### 内存和性能

事件委托：只需在 DOM 树中尽量高的层次上添加一个事件处理程序，由于所有需要处理的子节点事件都会冒泡，所以单击事件可以传递到这个程序中来处理。

事件委托的优点在于：

1. document 对象可以更快被访问到，无需等待节点的 load 事件；
2. 在页面中设置事件处理程序所需的时间更少。
3. 整个页面占用的内存空间更少，能够提升整体性能。

## Ajax

> Asynchronous JavaScript + XML 一种能够向服务器请求额外的数据而无需卸载页面的技术，带来更好的用户体验。
>
> 其核心是 XMLHttpRequest ( XHR ) 对象

#### XHR

1. IE7+ 以及其他浏览器中新建一个 XHR 对象： `var xhr = new XMLHttpRequest();` 
2. 调用 `open()` 方法，它接收三个参数：请求类型 `(GET / POST)` 、请求 URL、是否异步；
3. `send()`
4. Event Listener

```javascript
function reqListener () {
  console.log(this.responseText);
}

var oReq = new XMLHttpRequest();
oReq.addEventListener("load", reqListener);
oReq.open("GET", "http://www.example.org/example.txt");
oReq.send();
```

#### GET & POST

与 GET 请求相比， POST 请求消耗的资源会更多一些。从性能角度来看，发送相同的数据量，GET 请求的速度最多可达到 POST 请求的两倍。

#### 简单请求

(1) 请求方法是以下三种方法之一：

- HEAD
- GET
- POST

(2) HTTP的头信息不超出以下几种字段：

* Accept
* Accept-Language
* Content-Language
* Last-Event-ID
* Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

#### CORS Cross-ORigin Resource Sharing 跨域资源共享

整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。

因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

IE 浏览器中新建了 XDR 类型对象来实现 CORS 规范，其他浏览器都通过 XMLHttpRequest 实现了对 CORS 的原生支持。

#### JSONP

利用 `<script>` 标签 `src` 属性可以跨域访问的能力，在 URL 中传递一个前端参数，后端获取到请求后，将 JSON 格式的数据赋值给 URL 中的函数为参，用于前端 JS 调用显示。

JSONP 只支持`GET`请求，CORS 支持所有类型的HTTP请求。JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据。

#### Comet

 *Comet* 是一个 Web 应用模型，在该模型中，请求被发送到服务器端并保持一个很长的存活期，直到超时或是有服务器端事件发生。它是从服务器向网页推送数据的技术，适用于实时性要求比较高的应用。

有两种可以实现 *Comet* 的方式： **长轮询**和**流**：

长轮询的实现方法：

1. 可以使用 `script` 标签 (`JSONP`) ，正如 `iframe` 一样，其目标是把 `script` 标签附加到页面上以让脚本执行。服务器端则会：挂起连接直到有事件发生，接着把脚本内容发送回浏览器，然后重新打开另一个 script 标签来获取下一个事件。
2. 单纯的使用 `XMLHttpRequest` 对象来实现 HTTP 长轮询，打开一个到服务器端的 Ajax 请求然后等待响应，只要一有事件发生，服务器端就会在挂起的请求中送回响应并关闭该请求，然后客户端就会处理并使用这一响应，接着重新打开一个新的到服务器端的长生存期的 Ajax 请求。

流的实现方法：

1. 在 `XMLHttpRequest` 对象上使用某些浏览器（比如 Firefox）支持的 multi-part 标志。Ajax 请求被发送给服务器端并保持打开状态，每次有事件到来时，一个多部分的响应就会通过这同一连接来写入。通过侦听 `readystatechange` 事件以及检测 `readyState` 的值来处理响应数据以及维护请求。

#### SSE Server-Sent Events 服务器发送事件

是一种实现 Comet 交互的浏览器 API，支持轮询也支持 HTTP 流。SSE API 用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据给客户端。服务器响应的 MIME 类型为 `text/event-stream`。

```javascript
var source = new EventSource("/api/events")
source.onmessage = function(event) {
	console.log(event.data)
  	// 处理数据
}
```

#### Web Sockets

Web Sockets 是一种与服务器进行全双工、双向通信的通道。Web Sockets 不适用 HTTP 协议，而 Ajax 和 Comet 都是使用 HTTP 协议。这种协议专门为快速传输小数据设计，有速度上的优势。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于[服务器推送技术](https://en.wikipedia.org/wiki/Push_technology)的一种。

## JSON

#### 序列化与反序列化

2个程序（或服务器、语言等）需要交互通信的时候，JavaScript 里面提供了一个 `JSON` 对象，目前有2个静态方法:

- `JSON.parse`： 用来将 JSON 字符串反序列化成对象
- `JSON.stringify`：用来将对象序列化成 JSON 字符串

## XML

#### XML 是什么？

- XML 指可扩展标记语言（EXtensible Markup Language）
- XML 是一种*标记语言*，很类似 HTML
- XML 的设计宗旨是*传输数据*，而非显示数据
- XML 标签没有被预定义。您需要*自行定义标签*。
- XML 被设计为具有*自我描述性*。
- XML 是 *W3C 的推荐标准*

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```
