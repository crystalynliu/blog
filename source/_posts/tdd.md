---

title: TDD入门者的认知
date: 2019-04-21
categories: Methodology
tags: [agile, tdd]

---

TDD 作为敏捷开发中的重要环节，总是讨论的焦点，总是在说 TDD 这样那样。其实，我觉得大多数人对 TDD 的理解是模糊的，不明确的。从而导致没有人愿意有意识的去尝试使用 TDD, 去验证一下 TDD 到底会给我们带来什么？

我也是一枚初识 TDD 的菜鸟，所以想用自己的理解以及经历来解答上面的问题。

<!--more-->

# What's TDD?

先来看看 [Wiki](https://en.wikipedia.org/wiki/Test-driven_development) 的定义

> Test-driven development (TDD) is a **software development process** that relies on the repetition of a very short development cycle: requirements are turned into very specific test cases, then the software is improved to pass the new tests, only.
> 
> Test-driven development is related to the test-first programming concepts of extreme programming, begun in 1999, but more recently has created more general interest in its own right.

关注这段话中几个重要的点：

* **TDD 是一种方法论**，主要是驱使我们更早的去思考和设计应该怎样实现我们的程序。
* **TDD 是基于测试先行的一种重复开发的过程**，这里的测试指的是软件测试本身，包括基于代码单元的单元测试，基于业务需求的功能测试，以及基于特定验收条件的验收测试。

所以，本质上来说，TDD 被区分为两部分：**ATDD** 和 **UTDD**。

* **ATDD (Acceptance Test Driven Development)** : 基于验收测试的驱动开发，这种方式主要是由 BA 或者 QA 来编写测试用例，然后 Dev 通过验收测试来理解需求和验收条件，并编写实现代码直到验收测试用例通过。所以根据验收方法和类型的不同，ATDD 包括了 BDD（Behavior Driven Development）、EDD（Example Driven Development），FDD（Feature Driven Development）、CDCD（Consumer Driven Contract Development）等各种的实践方法。

* **UTDD (Unit Test Driven Development)** : 单元测试驱动开发，这种方式主要是  DEV 自己来编写单元测试用例，之后编写实现直到测试通过。

给了定义，再来看看区别，ATDD 本身更关注于能让 BA，QA 以及 DEV 在产品需求方面快速有一个统一的理解，而 UTDD 更关注于能快速的让 DEV 得到在程序设计方面的反馈。

而现在大多数人所认为的 TDD 只是单方面的理解为 UTDD。那接下来主要的关注点也就是备受争论的 UTDD。

# How to use UTDD?

UTDD 是一种依赖重复**特定开发流程**的方法论，学习怎么用 UTDD, 先要了解这种开发流程是什么样的。

## Test driven development cycle

![](/images/cycle.jpeg)

1. Add a test
2. Run all tests and see if the new test fails
3. Write the code
4. Run tests
5. Refactor code

Okay，过程是了解了，但是是不是只要重复这个过程就是 UTDD 了呢？答案并不是，UTDD 还有三条需要遵循的原则。

## Principle

* **Not allowed to write any production code unless it is to make a failing unit test pass.**

    "除非是为了使一个失败的 unit test 通过，否则不允许编写任何产品代码。"
    
    如果我们违反这个原则，先编写了实现代码，那这段实现代码是为了什么呢？怎么确保它真的实现了你的需求呢？基于需求的 test case 就是为了确保实现代码真正的实现了需求，而不是其他无用的代码。

* **Not allowed to write any more of a unit test than is sufficient to fail; and compilation failures are failures.**

    “在一个单元测试中，只允许编写刚好能够导致失败的内容（编译错误也算失败）。”
    
    如果写了多个失败的测试，测试总是无法通过会增加 developer 的压力。并且如果测试需要重构，也会增加测试成本。 

* **Not allowed to write any more production code than is sufficient to pass the one failing unit test.**

    “只允许编写刚好能够使一个失败的 unit test 通过的产品代码。”
    
    实现了超出当前测试的代码，这部分代码是没有测试保证的，那么不能保证其正确性，并且可能是不需要的需求，那就凭空增加了代码的复杂性。
    
总结来看 UTDD 的开发流程以及原则，本质上是为了

> 分离关注点，一次只戴一顶帽子

UTDD 中明确的三个步骤分别代表了三个关注点：**需求**， **实现** 以及 **设计**。

* 写一个失败的测试。是对需求的描述，是为了确认需求是什么，而不用关心怎么实现。
* 实现代码让测试通过。不用考虑需求是否符合预期，不用 care 代码写的多烂，只要写出让输入变成输出的逻辑就可以了。
* 重构代码。既不用考虑需求，也没有实现压力，更不用害怕重构会破坏原有的功能。找出代码的 bad smell，提高代码质量。 

# Why we need to use UTDD?

**Why** 翻译成中文是”为什么“，而我们再用这个词的时候，真正想知道应该是”什么“。”Why we need to use UTDD?“ 这句话在我看来实际就是一种**趋利**的表现，实际上真正表达的是”UTDD 能给我们带来什么好处“。

* **Not just simple validation of correctness, but can also drive the design of a program.**

    ”不仅仅是对正确性的简单验证，也可以驱动整个程序的设计“。
    
    怎么来理解这句话呢？UTDD 会要求我们先要写测试，先关注于测试的 case 都有哪些，那就说我们需要提前去考虑 client 端是怎么样调用 function 的。那就是说我们需要在实现之前先设计好 interface，思考需求合理行以及提前澄清需求。

* **Offers the ability to take small steps when required.**

    ”提供了可以小步开发的能力。“
    
    UTDD 会强迫将开发的步骤变小，在实现需求的时候，它只要求关注于怎么实现能通过当前的测试而不用关心其他异常情况的处理。这样被称作“小步快走”的开发，它会带来两个好处：
    * **让你的代码中不会有多余的无用代码**。无用代码指的是这段代码的删除并不会影响你的测试是否通过。
    * **节省了调试的时间与成本**。如果程序出现了问题需要调试，只用关注于刚刚为了通过测试而添加的代码，快速的定位到问题。
    
* **Offers enought tests to protect program and tests are document.**
    
    “提供了足够的测试保护程序, 以及测试即文档。”
    
    UTDD 是以测试先行的模式进行开发的，这样会保证
    
    * **所有的功能都会有测试来描述。** 不知道大家有没有过这样的经验，当你想要使用一个开源库的时候，发现它没有全面的 api 文档，不知道怎么它有哪些 api，不知道怎么使用它的 api。这个时候我就会去看 `test` 文件，通过 test 的用法以及描述，相当于我在看一篇详细的 api 文档。
        
    * **几乎100%的有效测试覆盖率。** 足够的测试覆盖率，会让我们很有自信地对代码设计进行重构，而不担心是否会破坏原有的功能，从而提高整体的代码质量，让其变得更加健壮以及可维护。
    
# Why we cannot use UTDD better?

总是有人在问，TDD 或者 UTDD 听起来这么有用，那为什么并没有很多人去用呢？对，可能大家或多或少都会听过 TDD，但是身边有几个人真正去使用过 TDD 呢？可能千分之一？可能万分之一。从我工作以来了解到的情况分析，原因不过于一下三点：
    
  * **陡峭的学习曲线**
    
    从 **How to use UTDD** 可以了解到，要使用 UTDD 进行开发，你需要很多基础技术的支持，怎么去 tasking 需求，怎么写 unit test 甚至于需要专门去训练怎么 refactor。就是说想要使用 UTDD 那就必须在短时间内学习以上的内容，我们都知道陡峭的学习曲线是很难去坚持的。
  
  * **急迫的快餐文化**
    
    上面说过了使用 UTDD 之前需要学习的内容，这样的学习压力会导致需要过多的投资付出，所以 UTDD 看起来是一种长期回报。而在今天的互联网时代，我们更加崇尚快餐文化，这种文化让我们不能很有耐心的看到 TDD 的回报，不认可 TDD 能带来更高的效率以及更高的质量。
    
  * **代码质量 vs 产品质量**
    
    到目前为止，没有任何一个事实能真正的支持代码质量和产品质量之前有着直接的因果关系。产品质量是什么，是产品有没有 bug, 而代码质量更多的关注于代码的可读性，健壮性以及可维护性。而 UTDD 带来的更多是相对于代码质量的好处。所以对于关注产品质量的角色来说，并不一定会认可这样的付出。
    
# End

看完上面👆这么多好与不好，总看起来像是纸上谈兵，不落地。也有人觉得 TDD 就是一种伪理论伪方法，并不会带来实质性的好处。我不推崇 TDD, 我也并不否定它，我觉得只有认真学习及实践过才有资格谈论好坏。

日本有一种剑道学习方法名为 [“守，破，离”](https://baike.baidu.com/item/%E5%AE%88%E7%A0%B4%E7%A6%BB?fr=aladdin)。**守** 即是先要虚心学习， **破** 即是当你学习熟练之后就可以挑战它摒弃糟粕，**离** 即是重新定义你学习的东西。

当我们大谈阔论 TDD 的时候，又有几个人真正地虚心学习过？

# Reference

* [《深度解读-TDD》](https://www.jianshu.com/p/62f16cd4fef3)
* [让我们再聊聊 TDD](http://insights.thoughtworkers.org/talk-about-tdd-again/)








