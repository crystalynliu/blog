---

title: 我所知道的 D3.js
date: 2019-01-09
categories: Coding
tags: [front-end, d3]

---

说起来当代社会有一个流行词语叫做**可视化**，以前我们是用纸笔，印刷技术来完成文字，数据的可视化。到了大数据井喷，人人都开始使用电子设备获取信息的时代，就需要使用技术来实现文字，数据的可视化。而在数据可视化中，最直观最有效的信息传递方式就是**使用图表的方式来展现**。

这里记录下来的是作为一个前端 developer, 在需要使用`D3.js`的去开发一个数据可视化界面设计时，所了解到的 `D3.js`。

这将成为一系列的文章，这是第一篇。

<!--more-->

# Introduction

## What's D3?

先来看看官方解释：
>D3 (or D3.js) is a JavaScript library for visualizing data using web standards. D3 helps you bring data to life using SVG, Canvas and HTML. D3 combines powerful visualization and interaction techniques with a data-driven approach to DOM manipulation, giving you the full capabilities of modern browsers and the freedom to design the right visual interface for your data.

看着是不是一脸懵😳。英语不好的话，可以参考一下我的翻译，官方发言说：

D3 是一个遵循现有的`web`标准上基于数据来操作 HTML DOM（Document Object Model）的 JavaScript 函数库（当然如果你不知道什么叫做 JavaScript，那你就可以关掉这篇文章了。），D3 用 SVG, Canvas 以及 HTML 把展示数据。D3 结合强大的可视化数据驱动技术来驱动 DOM 操作 ，给你强大的能力以及自由针对你的数据去设计更好的可视化界面。

了解过官方的定义之后，再来通俗的给大家一个解释。

D3的全称是 **Data Driven Documents**。懂点英语的都可以直译：**数据驱动文档**。其实简单来说，就是一个 JavaScript 的函数库，是用来在不借助任何框架的情况下，帮助你在现代浏览器里画出你想要的数据图表。

D3 是一个开源项目，托管在 Github 上, 目前 **Most stars 排名第8**, 可以看出这是个非常之受欢迎的函数库。那是因为它有着不可替代的优势。

## Advantage
* 它是一个 **jQuery like api 的函数库**，有前端基础的同学可以很容易使用它的 API ,链式操作以及事件处理。
* 它跟目前存在的图形库（`Echarts`之类）相比，**更底层**，那它就有**更大的灵活性**，可以给 UX 以及 developer 更大的发挥空间。

# How to use and learn it

## Installation

* 下载最新版本`.zip`(目前的版本已经更新到 V5)： [d3.zip](https://github.com/d3/d3/releases/download/v5.5.0/d3.zip)
* 页面插入如下代码，在线使用

	```js
	<script src="https://d3js.org/d3.v5.min.js"></script>
	```

* 如果在框架中使用（React, Vue, Angular等），使用 npm 安装

	```s
	npm install d3 --save
	```

## Learning

* 非常长的 [API document](https://github.com/d3/d3/blob/master/API.md)
* 成堆的 [tutorials](https://github.com/d3/d3/wiki)


# D3 with DOM
`Data Divern Documents` 的体现，在于 D3.js 这个函数库，可以针对输入的数据通过它的 API 方法去操作 DOM 元素（也就是 Documents, 来自于 HTML 概念）

## Selection

D3 提供了一种 API，被成为 `selections`，可以对 DOM 中的任意节点进行操作。D3 的 选择器是定义于 W3C 标准,可以使用的选择器包括：

* 标签（`"div"`)
* 类（`".awesome"`)
* Id (`"#id"`)
* 属性 (`"[color=red]"`)

以及更多的联合选择器以及伪选择器。

D3 目前提供了两种高级方法来匹配元素：

* `select()` ： 只返回第一个匹配的元素, 接收的参数可以是一个选择器字符串，也可以是一个节点
* `selectAll()`：选择在文档中遍历所有匹配的元素。接受的参数既可以是选择器字符串，也可以是一个节点（node) list。

D3 为匹配到的 node 提供了许多方法：

* 设置属性或者样式
* 注册事件监听器
* 添加、删除或排序 node
* 更改 HTML 或文本内容

例如：

```js
select("p").style("color", "white");
```

## Dynamic Properies

如果对其他 DOM 函数库（类似于 jQuery）比较熟悉的同学可以发现这样的操作和 D3是非常的相似。然而，在 D3 中，属性以及样式也可以**被指定为数据中的函数，并不仅仅只是简单的常量**。

例如，给每个段落随机的颜色：

```js
selectAll("p").style("color", function() {
	return "hsl(" + Math.random() * 360 + ",100%,50%)";
});

```

在 D3 中，绑定是一种比较独特的职责。**当数据被指定为一个数组，其中每个值都会被作为参数（d）传递到 selection 的函数中**。根据默认的索引排序，数据中的第一个值会传递给匹配到的第一个node, 第二个值会传递给匹配到的第二个 node, 以此类推。

```js
d3.selectAll("p")
	.data([4, 8, 15, 16, 23, 42])
	.style("font-size", function(d) {return d + "px"});
```

以上的代码说明，当一组数据绑定到 `p` 元素上，结果就是使用这些数据动态的设置每个 `p` 的字体大小。

D3 中提供了以下两个方法来绑定数据：

* datum()：绑定一个数据到选择集上
* data()：绑定一组数据到选择集上，数组中的各项值分别与匹配到的 element 绑定

## Enter and Exit

对于之前的示例，我们都假设 data 的个数以及匹配到的 element 个数相同。那 data 与 element 就可以一一对应的进行绑定。

但是，如果 data 和匹配到的 element 个数不相同的情况下，怎么办呢？

* `data.length > element.length`, 说明 DOM 元素少了，我们需要执行 `add elements` 的操作
* `data.length < element.length`, 说明 DOM 元素多了，那我们需要执行的是 `remove elements` 的操作。

那在 D3 中是通过什么样的操作来管理这两种情况呢？

* `.enter()` 指明了**所有没有匹配上 DOM 的数据集合**，随后结合`.append()` 操作，选择将这些没有匹配上的数据集合实例化。
* `.exit()` 返回**未能绑定到数据的 DOM 元素集合**，结合`.remove()`操作，来删除那些没有绑定上数据的 DOM 元素。

与以上两种操作相对应的是，`.data()`操作默认会**返回可更新的 DOM 元素集合**，所以说，如果你忘记了`.enter()` 以及 `.exit()` 操作，那么我们将自动获得已经与存在数据绑定上的元素。

```js
// Update...
const p = d3.select("body")
  .selectAll("p")
  .data([4, 8, 15, 16, 23, 42])
  .text(function(d) { return d; });

// Enter...
p.enter().append("p")
 .text(function(d) { return d; })

// Exit...
p.exit().remove()

```

通过对 selection 的一些操作描述，会发现这些操作，将选择的 DOM 元素分为了3组：

 * 需要创建的元素（加入组）
 * 需要更新的元素（更新组）
 * 需要删除的元素（退出组）

 ![](/images/bindingData.jpg)

总结得出，D3 使用这些操作，**实现了数据集到 DOM 元素实例化之间的平滑转换**。这发挥了提升性能最大化，并对转换有了更大的控制。实际上这种渲染方式与 React 管理子元素渲染的方式相似。

## Handling events

类似于 jQuery 这类的函数库，**D3使用 `selection.on()` 对 elements 添加事件处理函数，相应的数据（d）和索引（i）也将会当做参数传入到事件处理函数中进行处理**。事件有以下几种：`click`, `mouseenter`, `mouseover`, `mouseleave`, `mouseout` 以及 `mousemove`。

```js
d3.selectAll('rect')
  .on('click', function(d, i) {
    d3.select(this)
      .text('You clicked on rect' + i);
  });
```
这段代码实现当点击后就修改其 text 指明第几个元素被点击。


# End

当你看完了这篇文章，似乎觉得这些都是纸上谈兵。没错，这一篇 D3 的文章更偏于理论介绍, 是期望大家在看接下来的文章时，对 D3 已经有了大概的认知，那我们就更容易理解一些 Demo 以及其他的 API 概念。如果大家还对 D3 有兴趣，那么可以持续关注这一系列。
