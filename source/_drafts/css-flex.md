---

title: 布局神器，Flex
date: 2018-01-29
categories: 技术.改变生活
tags: [技术, 前端, css，布局]

---

有写过 `CSS` 的童鞋在面对布局问题的时，心里都在怒吼，为什么这个 `div` 就不能乖乖的在它自己的位置上。然而，一般引起布局与期望不同的原因一般有两个属性 —— “杀手” `float` 以及 “子不教父之过”的 `postion`。当然如果不知道的客官们，出门左转 Google。

为了让我们更高效更灵活的完成 UI WEB 的布局任务，某位不知名的大神，发明了 `Flexbox Layout`

如果客官们觉得我写的不够好，支持客官们出门右转去看专业介绍 [Flexbox Guide](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

<!--more-->

# Background OR Conception

## Flexbox Layout 诞生的原因
	
在这个年代，有几个人能说自己家里没有电脑，没有 pad, 没有手机。而对于做互联网产业的公司来说，怎么以最完美的方式将自己的产品展现在这些不同大小的屏幕上是一个不可避免的问题 —— 官话“**响应式布局**”。如果你问我，为什么这些公司不能给每一种大小的屏幕都做一种产品？我拒绝回答。这种费时费力吃力不讨好的事情，谁会干 —— 其实还是有人会干的 -_-
	
`Flexbox`被称作为“Flexible box”翻译过来就是**弹性盒子**，**主要思想**是让容器内部的所有子模块能更充分的利用容器内部的空间，在容器的宽高发生改变时，子模块可以保持对齐和平均的空间分布，以及进行缩小或者扩大，且不会发生溢出的情况。
	
听听，是不是感觉整个人生都美好了，But “响应式布局”并不仅仅是这样的。不过这是后话。

##最基本的术语
	
flexbox 的布局实现并不是一个模块上单独的一个属性，而是指结合多个模块的属性来实现的。有些属性是设置在父元素（被称为“flex container”）上，而另一些是设置在子元素（被称为“flex items”）上。
	
* Flex 的两根轴线
/Users/xqliu/Works/05-UI-homework/02-people-work/3weeks-3pages/3-Pages-in-3-Weeks-Episode-09/images/flexbox.png