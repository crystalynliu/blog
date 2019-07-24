---

title: D3.js 如何在 React 生态系统存活
date: 2019-01-12
categories: Coding
tags: [front-end, d3, react]

---

如果你对 `D3` 这个词语比较陌生，那你可以移步我的上一篇文章[《我所知道的 D3》](http://blog.xqliu.vip/2019/01/09/%E6%88%91%E6%89%80%E7%9F%A5%E9%81%93%E7%9A%84%20D3/)去了解一些关于 `D3` 的基础知识。

在这一篇继续学习 D3 中，我将会给出实例代码来展现 `D3.js` 是怎么样画出我们想要的图形，而根据现在的前端发展，又是怎么样和框架结合起来使用的呢？

<!--more-->

# D3，React 的冲突
## D3 与 DOM
对有了一定基础的同学，可以知道D3的绑定数据，其实就是一个使用 `selection` 链接数据到 DOM 元素的过程(这就是之前提到过的**enter-update-exit**)。

它帮助我们匹配已经创建DOM元素的数据，添加缺失的元素，删除不需要的元素。而它的这些操作，将选择的DOM元素分为了3个不同组：

 * 需要创建的元素（加入组）
 * 需要更新的元素（更新组）
 * 需要删除的元素（退出组）

 ![](/images/bindingData.jpg)

通过这样的描述，我们可以知道 D3 的绑定数据可以**实现数据集到DOM元素之间的平滑转换**。实际上这样的渲染方式和 React 的子元素渲染方式非常的像。

## React 与 DOM
React 是一个 JavaScript 库，它可以让我们通过组合组件来构建用户界面。并且使用虚拟 DOM（DOM当前状态的一种表示）和一个复杂的 diff 算法来理解当条件发生变化时，页面的哪部分组件需要重新渲染。而**这个diff算法需要根据传递给组件的新数据（或者 state），来判断是否需要重新呈现该项，或是否需要添加或删除该项**。

听起来似乎和D3的数据绑定模式很相似？

## D3 与 React
通过上面👆的了解，React 和 D3 都有共同的目标：**以高度优化的方式处理 DOM 及其复杂性**。而且还有共同的爱好：**对纯函数的偏好** 以及 **无状态组件**

>题外话，什么是纯函数？
>
>对于给定的输入，它总是返回相同的输出而不会产生副作用的代码

但是，问题就来了，对于 DOM 的共同关注使得这两个库在使用的时候发生了冲突，到底应该使用哪个来渲染界面 DOM 元素呢？我们将给出一些解决的方法，似乎都不简单。但是，我们有一个硬性的规则：

**它们永远不应该共享 DOM 控制**

# 解决的途径

## D3.js within React
针对于这种方法，主要的思想是**尽可能多地为 D3 代码提供控制**，React组件会渲染一个空的SVG元素，用作数据可视化的根元素。然后，在componentDidMount生命周期方法中，使用D3.js代码在根元素创建图表。为了阻止 React 控制更新，可以通过使shouldComponentUpdate方法始终返回false来阻止任何进一步的组件更新。

```
class Line extends Component {

	componentDidMount() {
		// D3 code to create the chart
		// using this._rootNode as container
	}
	shouldComponentUpdate() {
		return false;
	}

	_setRef(componentNode) {
		this._rootNode = componentNode;
	}

	render() {
		<div ref={this._setRef.bind(this)} />
	}
}
```
优点：

* 这是一个最简单的解决方案，大部分情况都可以正常工作
* 很方便的可以将已经写好的 D3 代码移植到 react 中

缺点：
* 混合使用 D3.js, 会让代码包含太多依赖项，并使得文件太长，不能体现出组件化的好处

## 生命周期的封装
就是基于类 React 的生命周期方法，去将 D3 图表的创建、更新和删除包装起来，划定一个清晰的边界。

这种方法我并没有尝试过。但是这种策略促进了结构的分离，并且很容易让 react 和已经写好的代码进行集成。主要缺点在于**不可能实现服务端的渲染**。

## React 操作 DOM， D3 用来计算
这种策略的想法是**将 D3 的使用限制到最低的程度**。这意味着通过执行SVG 路径、布局以及任何转换的计算，可以将用户数据转换为可以用 React的方法来绘制内容。

```
class Line extends Component {
	drawLine() {
		const xScale = d3.scaleTime()
				.domain(d3.extent(this.props.data, ({date}) => date))
				.rangeRound([0, this.props.width])
		const yScale = d3.scaleLinear()
				.domain(d3.extent(this.props.data, ({value}) => value))
				.rangeRound([this.props.height, 0]) //这个值跟 svg 的坐标系原点有关

		const line = d3.line()
			.x((d) => xScale(d.date))
			.y((d) => yScale(d.value));

		return (
			<path
				className="line"
				d={line(this.props.data)}
			/>
		);
	}

	render() (
		<svg
			width={this.props.width}
			height={this.props.height}
		>
			{this.drawLine()}
		</svg>
	)
}
```

这目前是**对 React 最友好的实现方式**，它的实现方式几乎与 React完全一致，并且可以将每一个部分的图形，拿出来单独的做组件化。另一个好处是在服务端进行渲染，也可以用来 React Native 上（并没有验证是否可行😁）

但是它的缺点也是毋庸置疑的，就是原本很多 D3 可以帮我们完成的工作，我们需要自己来完成(例如缩放，拖动以及动画等复杂功能)，这意味着你可能需要大量的工作来做本来 D3 就可以完成的功能。

# 我们简单的来画一个柱状图
针对以上的了解，我们在实际代码中看一下，D3 以及 D3 与 React 结合的方式来渲染图形的区别。

![](/images/bar.png)

我们期待的输入数据为：

```
const data = [
  { city: '北京', amount: 1000, },
  { city: '上海', amount: 803, },
  { city: '广州', amount: 440, },
  { city: '深圳', amount: 780, },
];
```
在开始写代码之前，我们需要了解 bar 图的组装部分，都需要使用什么样的图形来组装：

* X轴，对应 city, 决定每个 bar 的 x 位置
* Y轴，对应 amount, 决定每个 bar 的高度，即 y 的位置
* Bar, 如果使用直角 bar, 可以直接使用 `rect` 元素，或者其它要求的话(例如圆角)，可以使用 `path`

那在选择用什么方式去实现代码之前，我们要完成的就是数据的输入值与 container 宽度以及高度的映射。

```
var width = 500;
var height = 600;

var xScale = d3.scaleBand()
  .range([0, width])
  .domain(['北京', '上海', '广州', '深圳'])
  .padding(0.2);

var yScale = d3.scaleLinear()
  .range([height, 0])
  .domain([0, 1000]);

```

**注意这个`range([500, 0])`,之所以是反向的，是因为 svg 的坐标系的（0，0）点是在左上角，意思就是纵向坐标是反向的。**

## D3 的实现
我们默认页面中只有一个根元素`body`, 会使用 `D3` 根据数据来渲染出想要的图形元素。

```
var svg = d3.select("body")
  .append("svg")
  .attr("width", width)
  .attr("height", height);

var rects = svg.selectAll("rect")
  .data(data)
  .enter()
  .append("rect")
  .attr("x", function(d){
      return xScale(d.city);
  })
  .attr("y", function(d){
      return yScale(d.amount);
  })
  .attr("width", xScale.bandwidth())
  .attr("height", function(d){
      return height - yScale(d.amount);
  })
  .attr("fill", '#bada55')
  .exit()
  .remove();
```

## D3 与 React 的实现

默认的情况，是已经有了一个 svg 的元素被创建，它里面会包含一个子组件，`Bar`

```
import * as d3 from 'd3';
import React, { PureComponent } from 'react';

class Bar extends PureComponent {

  render() {
    return (<svg width={width} height={height}>
      {data.map(({ city, amount }, index) => (
        <rect
          key={index}
          x={xScale(city)}
          y={yScale(amount)}
          width={xScale.bandWidth()}
          height={height - yScale(amount)}
          fill={'#bada55'} />;
      )}
    </svg>);
  }
}
```

这样就完成了一个最简单的 bar 图，可以看到出来的结果中，只会出现 bar，并不会有 x轴，以及 y轴，那这些要怎么样来完成呢？那什么是 D3 layout, D3 shape 呢？期望大家可以自己去了解和思考🤔。

#End
我们一般工作中构建图表，要考虑的因素有时间，成本以及质量。这个时候就需要 developer 自己来判断什么样的方式是合适的。

事实上，当写完这篇文章的时候，我觉得我的这两篇文章并不能让大多数人理解 D3。或许我应该去写一个系列。将D3 的概念拆分出来一一讲解。（希望我有时间）。
