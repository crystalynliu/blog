---
title: 样式包裹的 React 组件 - Styled Components
categories: Coding
tags: [front-end, react, css]
---

自从有了 web 应用开发以来，前端的工作总是离不开 CSS 样式的实现，它可以让我们的 web 应用变得更漂亮，布局更合理。但是前端开发程序员们发现他们总是在更漂亮的页面和可维护的 CSS 代码中挣扎，从而促进了 CSS 在编程方面的发展。

在当前的前端发展中，CSS in Javascript 是很难让人忽视的，是一个很热门的趋势。那是否应该应用到项目中呢？我们来谈谈其中的一种 `Styled Components`。

<!--more-->

# CSS Evolution

## Crazy CSS

当 web 应用出现的时候，我们期望通过一种方式给页面添加一些不同的元素，颜色，gif 图片，超链接等等，来吸引人的注意力以提高整个网站的访问量。那个时候并没有人在意实现方式是否合理，只需要效果达到预期就好，那个时候 `inline style` 可能就足够了。

但是当网站的体量变大，需要跟多的开发人员来维护页面样式的时候，每个人对 CSS 的实现或者样式有不同的见解，这时候为了表达实现样式意愿的强烈，大家开始使用 `important!`。

**How is it terrible!** 😱

很快我们就发现，每个 CSS 样式后面都会有 `important!`, 我们再也没有办法清理无用的代码。而对后续的开发和维护造成了很大的问题。

## Salvation of Sass

Sass 的出现像是给了前端程序员的救赎，它是一种 css 预处理引擎。它实现了**变量**，**嵌套**，**mixin** 以及继承，能让你在更好的组织实现 css, 并且将 css 代码以小模块的方式来结构。很大程度上解决了 css 难以维护的问题。

但是，Sass 是预处理引擎，本质上是将其编译成 css 之后，捆绑打包到全局的输出文件中，这种方式真的好吗？我们并没有解决问题，只是依靠减少嵌套来优化代码可读性，但是编译过后的 css 代码一样惨不忍睹, 会有各种各样的优先级问题。

## Arrival of BEM

当 BEM 到来的时候，我们第一次将样式的实现与 component 联系起来。将 css 语义化带入了一个新的 level, 我们可以利用简单的块级修饰符来确保 class name 的唯一性，解决 css 之间优先级冲突的问题。BEM 是一种可以 css 模式变得更显而易见很好的方式。

但是它也带来的新的问题：

- 为 class 名字变成了一个很繁重的任务
- 这种很长的命名使 html 标签变得很臃肿
- 如果你想要复用，你需要显示的表明 component 的继承关系

## CSS Modules

Sass 和 BEM 都没有实际上实现 css 的封装概念，只是依赖于开发人员选择唯一性的类名，这更像是一种约定。但是我们可以依赖工具来完成，这就是 `CSS-Modules`。它依赖于为每个 css 样式动态的创建一个类名来确保所有的 css 样式都被封装。

可以显而易见的是，它迅速的在 react 的项目中变得流行起来。但它并不能很好的易于复用和控制，它像是一个聪明的 `BEM` 自动化命名工具，生成名字很长并且不易于调试。

综上，每一种工具的产生都有自己意义与想要解决的问题。当问题并没有被已经产生的工具所解决的时候，就会发展出新的工具 -- **Styled Components**.

# Styled-Components

## Concept

> styled-components utilises tagged template literals to style your components.
>
> It removes the mapping between components and styles. This means that when you're defining your styles, you're actually creating a normal React component, that has your styles attached to it.

它的核心思想就是**样式和 tag 标签（或者其他组件）可以变成一个 React 包裹组件，将其子组件一起包装起来映射到真实的 html 标签上， 删除了组件和样式之间的映射关系。** 这句话一眼看起来还是很抽象，我们可以从例子中慢慢理解。

```Javascript
import React from "react"
import styled from "styled-components"

// 生成一个 Title 组件，它渲染了一个带样式的 <h1> 标签。
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

// 然后像使用其它的 React 组件一样使用它。
render(
  <Title>
	Hello World!
  </Title>
);

// 实际上它就编译成了👇

<h1 class="dxLjPX"> Hello World! <h1>

```

## Motivation

> styled-components is the result of wondering how we could enhance CSS for styling React component systems.

目前 **Styled Components 只支持在 React 生态中使用**。

那 `styled-components` 有哪些优势？

- 自动关联 css 样式：可以持续跟踪页面上加载出来的组件，并为它注入样式。这也就意味着，用户只用 load 最少的样式代码
- 不用担心 class 名字：能为所有的样式构建唯一的 class 名字，不用担心重复和拼写错误的问题
- 容易删除不用的 class: 因为组件和样式是绑定在一起的，一但组件不被使用并删除掉，它相关联的样式也会被删除
- 主题相关：可以很方便的使用主题调整组件的样式
- 更容易维护：样式总是和组件在一起，不需要去别的文件寻找样式
- 自动添加前缀：只用编写标准 css, 其他的由 styled-component 来负责

## Basic

### Styling for any components

styled-components 可以适用于任何 component, 不管是 HTML Dom 元素还是任何第三方定义的 component（只要 className props 被传递给了 dom 元素），都可以用它包起来组成一个样式组件。

```javascript
// If you import a Link componet from `react-router-dom`, you can give style for it as following:

const StyledLink = styled(Link)`
  color: palevioletred;
  font-weight: bold;
`;

// Use for HTML dom

const StyledDiv = styled.div`
  color: palevioletred;
  font-weight: bold;
`;
```

根据以上的例子，我定义了两个 styled-components, 之后，在 react render 方法里就可以按照正常的 component 来使用。**当使用这种方式定义 component 的样式时，就相当定义了一个正常的 React component。**

### CSS function based on props

当我们给 styled-components 创建的 component 传入 props 的时候，我们可以在定义 styled component 的时候，使用函数来调整样式，props 将作为这个函数的参数。

```
const StyledDiv = styled.div`
  background: ${props => props.primary ? "palevioletred" : "white"};
  color: ${props => props.primary ? "white" : "palevioletred"};
  width: 200px;
  height: 200 px;
`;
render(
  <div>
    <StyledDiv>Normal</StyledDiv>
    <StyledDiv primary>Primary</StyledDiv>
  </div>
);
```

根据上面 👆 这个例子，通过传入 props 的值 `primary`, 我们在样式模板里根据 props.primary 的值来定义组件样式的变化。

这样当一个组件需要 JS 的条件变化来定制组件样式的不同时，styled-components 将控制权交给了 css 本身，而不是通过多个条件渲染不同的类名来变化。styled-components 这种机制，很容易实现组件主题的定制。只用在根组件上出入主题 props ，那么主题就会自动注入到所有子组件上。

**Notes:** 样式模板里面的函数要怎么写单元测试呢？参考 [jest-styled-components](https://github.com/styled-components/jest-styled-components)

### Extending styles

当设计变得多样性的时候，我们会发现很多组件的样式大部分相同，只需要一点点改变的时候，我们就会想念 extend 继承的概念。当然你可以用不同的 props 生成不同的样式，但我们不建议这么做，这样做会增加组件复杂性。当我们只需要静态的样式时，就可以选择继承来复写样式。

```
const StyledDiv = styled.div`
  background: "white";
  color: "palevioletred";
  width: 200px;
  height: 200 px;
`;

const PrimaryDiv = styled(StyledDiv)`
  background: "palevioletred";
  color: "white";
`;

render(
  <div>
    <StyledDiv>Normal</StyledDiv>
    <PrimaryDiv>Primary</PrimaryDiv>
  </div>
);
```

以上的例子会生成另外一个 div component，但仅仅只改变了背景色和字体颜色。

还有很多其他的用法，例如

- 怎么传递 props
- 如何支持 typescript
- 怎么定义全局主题（这是我最喜欢 styled component 的部分）

大家可以自行上官网查询 [https://styled-components.com/docs](https://styled-components.com/docs)

# Reference

- [CSS Evolution: From CSS, SASS, BEM, CSS Modules to Styled Components](https://medium.com/@perezpriego7/css-evolution-from-css-sass-bem-css-modules-to-styled-components-d4c1da3a659b)
- [Styled Components: To Use or Not to Use?](https://medium.com/building-crowdriff/styled-components-to-use-or-not-to-use-a6bb4a7ffc21)
