---

title: Flex，的全面理解
date: 2018-08-29
categories: Tech.change.life
tags: [technology, front-end, css]

---

对于刚刚接触 `CSS` 的码农们在面对布局问题的时，心里都在怒吼，为什么这个 `div` 就不能乖乖的在它自己的位置上。然而，一般引起布局与期望不同的原因一般有两个属性 —— “杀手” `float` 以及 “子不教父之过”的 `postion`。

<!--当然如果你不知道，那可以关注基础篇《css 大杂烩》。-->

为了让我们能够更高效更灵活的完成 Web 页面中的布局，出现了`Flexbox Layout`。

<!--more-->

---

# Background

在现代，所有展示在浏览器的产品，都逃脱不了，css 布局（layout）的魔咒，甚至现在为了兼容手机，pad 端的显示，还要完成响应式布局。当然了，今天的重点还是在，**布局**。

给大家一个我们常用的网页布局列表

![](/images/traditional-layout.gif)

感觉大家脑海里都疯狂的在想怎么用 `float`，怎么用`postion`，还要考虑怎么解决被他们引起的父级元素塌陷问题，想要知道解决方案的可以看[这里](http://www.ruanyifeng.com/blog/2009/04/float_clearing.html)。

没错，最原始的 layout 解决方案，我们都是基于 [css 盒模型](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model), 依赖 `display`，`float` 以及 `position`属性，来实现的。但是用他们来解决**垂直居中**以及**响应式布局**就显得尤其地不方便。

那，2009年 W3C 就提出了一种新的方案 -- **Flex layout**，它可以用简洁地代码快速响应各种的页面布局。目前已经被主流的浏览器所兼容，当然如果你不是很确定，可以使用 [Can I Use](https://caniuse.com/#search=flex) 很方便的查找它的兼容性。

---

# What's the Flex?

`Flexbox` 完整的来说叫做 **Flexible box ("弹性布局")**, 意思是用来为盒模型提供最大的灵活性。

>Aims at providing a more efficient way to lay out, align and distribute space among items in a container, even when their size is unknown and/or dynamic（thus the word `flex`）
>
>The main idea behind the flex layout is to give the container the ability to alter its items' width/height (and order) to best fill the available space (mostly to accommodate to all kind of display devices and screen sizes). A flex container expands items to fill available free space, or shrinks them to prevent overflow.

（英语不够好，不要怪我咯~）

其主要思想是**让容器内部的所有子模块能更充分的利用容器内部的空间**，在容器的宽高发生改变时，子模块可以保持对齐和平均的空间分布，以及进行缩小或者扩大，并且不会发生溢出的情况。

而对于任何一个 DOM 元素来说，都可以指定为 Flex 布局。

需要注意的是，父元素设置为 Flex 之后，子元素的 `float`, `clear` 以及 `vertical-align` 属性就失效了。

---

# Basics

`Flexbox` 的布局实现并不是一个模块上单独的一个属性，而是指**结合多个模块的属性来实现的(一般指父子模块的组合)**。有些属性是设置在父元素（`flex container`）上，而另一些是设置在子元素（`flex items`）上。

上面我们介绍过，传统的 css 布局，是依靠 `display` 的属性值：`block` 和 `inline`, 并且会依据从左向右，从上到下的方向来布局（当然使用一些特殊的 css 属性会改变这一顺序，例如 `float`，`position`）。

但是`flex`布局是有自己的一套布局流方式，称为`flex-flow directions`。就是说 **flex 布局内部的 items 会根据主轴（`main axis`）的方向(从 `main-start` 到 `main-end`)以及交叉轴（`cross axis`）的方向（从`cross-start` 到 `cross-end`）来进行放置的**。我会利用下面👇的图，来详细解释这个概念。

![](/images/flexbox.png)

* `flex container`: 指标记为`display:flex`的 DOM 元素。
* `flex item`: container 内部的**直接子元素**。
* `main axis`: container 内**主轴**，当然，不要看这个图就认为主轴的方向永远是水平的。我们可以通过`flex-direction` 这个属性将主轴的方向改为垂直的。
* `main-start` | `main-end`: **items 在 container 内部放置时的位置总是从 `main-start` 开始 到 `main-end` 结束**。从这个图上来看，是从左到右的。但我们还是可以通过 `flex-direction` 来改变它的方向。可以变成从右到左，从上到下，从下到上。
* `main-size`: **单个 item 占据主轴的空间**，有可能是指宽度（`width`）或者高度（`height`）-- 这个因为主轴有可能是水平的，也有可能是垂直的
* `cross axis`: container 的 **交叉轴**，**永远与主轴垂直正交**，所以说他的方向是依赖于主轴的方向。
* `cross-start` | `cross-end`: **items 在 container 内部是按照从`cross-start`的位置开始到`cross-end`的位置结束的**，这个方向也不会是永远固定的，依赖于主轴的方向。
* `cross-size`: **单个 item 在交叉轴上占据的空间**，宽度（`width`）或者高度（`height`）都有可能。

---

# Properties for container

Flex 提供了7个属性设置在 container 上。

* display
* flex-direction
* flex-wrap
* flex-flow
* justify-content
* align-items
* align-content

## display

这个属性被设置为`flex`或者`inline-flex`的 DOM 元素被定义成 flex container。这个 container 内部的直接子元素都会拥有 flex 的上下文，都会变成 flex items.

```css
.container {
  display: flex; /* or inline-flex */
}
```

## flex-direction

它建立了整个 flex 上下文中的主轴（`main-axis`），从而定义了放置 items 在 container 中的方向。

```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

![](/images/flex-direction.svg)

* `row`(默认值)：
	* 在设置了属性`ltr`的情况下，`main-start` 在左，`main-end`在右
	* 在设置了属性`rtl`的情况下，`main-start` 在右，`main-end`在左
	* 交叉轴（`cross-axis`）方向是从上到下
* `row-reverse`：
	* 在设置了属性`ltr`的情况下，`main-start` 在有，`main-end`在左
	* 在设置了属性`rtl`的情况下，`main-start` 在左，`main-end`在右
	* 交叉轴（`cross-axis`）方向是从上到下
* `column`：
	* `main-start` 在上，`main-end`在下
	* 交叉轴（`cross-axis`）方向是从左到右
* `column-reverse`：
	* `main-start` 在下，`main-end`在上
	* 交叉轴（`cross-axis`）方向是从左到右

## flex-wrap

在默认的情况下，items 会尝试在 container 里面放在一条轴线上，不会换行显示。你可以改变 `flex-wrap` 的值来使 items 换行显示。

![](\images\flex-wrap.svg)

```css
.container {
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

* `nowrap`（默认值）：所有的 items 都会强行放在一行（列）
* `wrap`：当 items 在一条轴线显示不下的时候会自动放到多行（列）上
	* 主轴是水平的，多行排列就是从上到下
	* 主轴是垂直的，多行排列就是从左到右
* `wrap-reverse`：items 在一条轴线显示不下的时候会自动放到多行（列）上，但是排列顺序与 `wrap` 相比是相反的
	* 主轴是水平的，多行排列就是从下到上
	* 主轴是垂直的，多行排列就是从右到左

## flex-flow

这个属性是 `flex-direction` 和 `flex-wrap` 的结合起来的简写模式。默认值是 `row nowrap`。

```css
flex-flow: <'flex-direction'> || <'flex-wrap'>
```

## justify-content

这个属性定义了主轴（`main-axis`）方向上，items 的对齐方式。并且可以帮助 container 去分配除过 items 所占有的剩余空间。

![](\images\justify-content.svg)

```css
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
}
```

* `flex-start` (默认值)：从主轴的起点（ `main-start`的位置）开始放置 items
* `flex-end`：从主轴的起点（`main-end`的位置）开始放置 items
* `center`：items 沿主轴居中
* `space-between`：items 均匀分布在主轴上，第一个 item 在 `main-start` 位置，最后一个在 `main-end` 位置
* `space-around`：items 均匀分布在主轴上，每个 item 周围分配的空间相等。虽然从视觉上，看出来并不是这样的，因为项目之间的间隔是两份空间，项目和边框之间的空间只有一份。
* `space-evenly`：items 均匀分布在主轴上，所以任意两个 item 之间的间距（包括 item 与边框的间距）都相等。这个可以对比一下 `space-around`一起理解，并且目前支持它的浏览器版本有限。

## align-items

这个属性定义了 items 在交叉轴（`cross-axis`）上的对齐方式，功能几乎等同于`justify-content`, 只不过是针对交叉轴（`cross-axis`）。

![](\images\align-items.svg)

```css
.container {
  align-items: stretch | flex-start | flex-end | center | baseline;
}
```
* `stretch`（默认值）：拉伸 item 的宽度或高度去填充 contianer. 仍然遵循 `max-width`(`max-height`) 以及 `min-width`(`min-height`)
* `flex-start`（默认值）：从交叉轴的起点（`cross-start` 的位置）开始放置 items。
* `flex-end`：从交叉轴的终点（`cross-end` 的位置）开始放置 items。
* `center`：items 沿交叉轴居中
* `baseline`：items 是按照 item 第一行文字自身的基线来对齐的。（这个听起来有点抽象，之后会查些资料做补充）

## align-content

当交叉轴（`cross-axis`）还有多余空间时，将 container 中的多行进行对齐，类似于对单个 item 在主轴上的 `justify-content`.

![](\images\align-content.svg)

```css
.container {
  align-content: flex-start | flex-end | center | space-between | space-around |stretch;
}
```

* `flex-start`：从交叉轴的起点（`cross-start` 的位置）开始放置多行 items。
* `flex-end`：从交叉轴的终点（`cross-end` 的位置）开始放置多行 items。
* `center`: 多行的 items 都放置在交叉轴的中间。
* `space-between`：每一行的 items 在交叉轴上平均分配空间，第一行在交叉轴的起点，最后一行在交叉轴的终点。
* `space-around`：每一行均匀分布在交叉轴上，之间的空间大小相等。
* `stretch`（默认值）：每一行都被拉伸至沾满整个交叉轴。

---

# Properties for items

Flex 提供了6个属性设置在 container 上。

* order
* flex-grow
* flex-shrink
* flex-basis
* flex
* align-self

## order

默认情况下，items 的顺序是根据代码里面的 DOM 顺序来排列的。然而，order属性是可以控制 items 在 flex container 的顺序。**数值取整数，值越小，排列越靠前。** 默认值为0。

![](\images\order.svg)

```css
.item {
  order: <integer>; /* default is 0 */
}

```

## flex-grow
这个属性定义了 item 在必要时的增长能力，就是**放大比例值**。

**默认值是0，表示就算用剩余空间也不会放大。**

如果所有的 items 的 `flex-grow` 都设为1，那 contianer 里面剩余的空间会等分给所有的 items。但是如果其中一个 item 的值被设为 2，那它占用的剩余空间是比别的 items 的两倍。(负数是不合法的)

![](\images\flex-grow.svg)

```css
.item {
  flex-grow: <number>; /* default 0 */
}
```

## flex-shrink

这个属性定义了 item 在必要时的收缩能力，就是**缩小比例值**。

**默认值为1，表示一旦空间不够，item 就会缩小。**

如果所有的 items 的 `flex-shrink` 都设为1， 当 container 里的空间不足时，items 都将等比例缩小。但是如果其中一个 item 的值被设为0，那它将不会被缩小。(负数是不合法的)

```css
.item {
  flex-shrink: <number>; /* default 1 */
}
```

## flex-basis

这个属性定义了**剩余空间分布之前 item 的默认大小，可以是一个长度（例如20%，5rem，等等）或者关键字。**

* `auto` 关键字的意思是指 “请查看我的宽度或者高度”(这是由`main-size` 关键字临时完成的，直到弃用)。

* `content` 关键字的意思是“ 根据item 的内容调整大小”（目前还没有得到很好的支持）

```
.item {
  flex-basis: <length> | auto; /* default auto */
}
```

注意，当这个值为 `0`，那 item 将不会分配 container 剩余空间，如果是 `auto`, 剩余的空间将按照 `flex-grow` 的值来分配。

## flex

这是 `flex-grow`, `flex-shrink` 和 `flex-basis` 的组合简写方式。其中，第二个和第三个值是可选的。

**默认值是 `0 1 auto`**

```css
.item {
  flex: none | [<'flex-grow'> <'flex-shrink'>? || <'flex-basis'>]
}
```

官方推荐是使用这个简写的方式，因为它会默认的设置其它两个值。

## align-self

这个值允许为**单个 item 设置对齐方式**，会覆盖默认的对齐方式（或者 `align-items` 设置的对齐方式）


![](\images\align-self.svg)

```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

* `auto`（默认值）：表示继承父元素的 `align-items`, 如果没有父元素，等同于 `stretch`。
* 各项参数可以参考 `align-items` 的用法。

---

# End

Finally, 列举完了所有的属性。主要是依靠着自己的理解以及对比参考了相关文章。其中，要注意的是**平时我们使用的 `float`，`clear` 以及 `vertial-align` 都不会对 flex item 产生影响。**

所以有了 `flex`，什么样的布局，你都可以仅仅使用几行命令就可以搞定。本来应该再有一篇实践篇，但是发现了 [阮一峰:《Flex 布局教程：实例篇》](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)，已经很详细了，我就不再画蛇添足了。

## 参考文章
* [《阮一峰: Flex 布局教程：语法篇》](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
* [《A Complete Guide to Flexbox》](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)