title: 【翻】2015年JavaScript应用架构趋势
date: 2014-12-16 12:00:47
categories:
- Translation
tags:
- javascript
- architecture
---
翻译一篇最近被很多前端业内人士转发的文章，主要是学习一下当前前端趋势中的一些概念，同时也顺便分享一下好文。

# 2015年JavaScript应用架构趋势

我曾经告诉过某人我是一名架构师，恩，这是事实。。因为现在我不得不编造无数个精心构造的谎言去证明我确实是个架构师。好。。说正经的，我认为在即将步入2015年之际，谈一点有关JavaScript社区的应用架构方面的进展的话题多少是有点好处的。在本文中我将就组合化（composition），功能性边界（functional boundaries），模块化（modularity），不可变数据结构（immutable data structures），CSP通道（CSP channels）以及一些其他相关的话题谈谈我的见解。

author: [Addy Osmani](https://medium.com/@addyosmani)
translator: [melon](https://github.com/melon)
original url: [JavaScript Application Architecture On The Road To 2015](https://medium.com/@addyosmani/javascript-application-architecture-on-the-road-to-2015-d8125811101b)

## 组合化

在架构方面，我们构建大型JavaScript应用的方式在过去几年中在至少一个基础的方面发生了变化。当你试图摆脱繁琐的机械化操作而引入了**单向数据绑定（unidirectional data-binding）**，**不可变数据结构**以及**虚拟DOM（virtual-DOM）**（这三个都是非常有趣的技术）时，这其中传递出来的一个关键的概念就是**组合化**，组合化正是众多开发者们竞相追求的一个目标。组合化强大得不可思议，它让我们可以将可复用的功能组件粘贴起来从而“组合化”成一个更大的应用。组合化传递出一种思想，模块化的东西是好的，因为它们体积小、易于测试，易于和别人讨论，还有易于发布。妈蛋，只要看看采用模块化的node工作得多“和谐”你就知道了！

![组合化的一个例子：图中的应用是由一些稍小的可复用的UI组件组合而成的，而这些UI组件本身由扩展或复用当前的一些组件或库实现的](https://d262ilb51hltx0.cloudfront.net/max/1764/1*8fuXs-f2LJHYnEfxGKYdLA.png)

组合化正是导致关于React的组件，Ember的组件，Angular的directive，Polymer的元素以及原生的Web Components的话题最近很热的一个原因。我们或许会争论这些框架和库的风格孰优孰劣，但是我们却无需去怀疑组合化的这个方向是不是不对。注意：早期的一些玩JS框架（Dojo,YUI,ExtJS）的开发者曾极力推荐过组合化的概念，组合化的概念也出现很久了，但是直到最近大部分人才开始领会这种模型在前端开发方面的强大威力。

**组合化是一种用来应对应用复杂化问题的解决方案。**当前，web平台中的语言的演化都是朝着缓解当前复杂应用开发所带来的痛苦这一目标努力的。复杂本身有很多种形式，但是当我们观察一下过去几年中开发者们构建web应用的局面，就可以发现共同模式（common patterns）是很明显值得我们考虑围棋构建解决方案的一个方向。这就是为什么让Web Components概念被广大浏览器厂商所认同是一件相当重要的事情。即便你压根不乐意用上面提到的那些不同风格的框架和库（我会告诉你它们真的很强大），我也希望你能找到自己喜欢的组合化的解决方案。

我希望未来能发生的是状态同步（state sychronisation）（同步你的model/server与组件中的DOM两者之间的状态）的改进以及充分发挥组合边界（composition boundaries）的威力。

### 组合边界

如果在讨论web开发的组合化时，不提一下[影子DOM](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/)（Shadow DOM）似乎说不过去。我们最好可以这么想组合边界这个feature：它可以在用标准HTML构建的组件之间提供真正的功能性边界。这就可以让我们知道一个组件在哪里结束、另一个组件又在哪里开始，可以让我们终于可以和一个组件污染整个页面的问题说拜拜了。另外，组合边界也可以让我们缩小CSS选择器的匹配范围，从而让深层组件结构中的元素能避免去进行开销巨大的样式规则匹配过程。

![“影子边界”是一道用来区分常规DOM（轻DOM）和“影子DOM”的屏障](https://d262ilb51hltx0.cloudfront.net/max/1464/1*8wTmS1mjdJ6EaaFPWkYNhg.png)

如果你还尚未接触过“影子DOM”，让我来解释一下，“影子DOM”可以允许浏览器在一个文档的渲染中嵌入一个DOM元素组成的子树（例如这个子树可以用来表示一个组件），但是并不将其真正嵌入到页面主文档的DOM树中去。这就在一个组件和另一个之间构建了一个**组合边界（composition boundary）**，根据这个特点我们不难理解：组件可以看做是一些体积小的、能不用iframe就能实现的独立的区块。支持影子DOM的浏览器可以实现在这些边界上轻松跨越，但是在每个组件内部，也就是DOM子树内部，我们还是可以像写正常的HTML语句一样来编写。**这其实上也实现了将影子DOM内部的组件实现细节和组件外部隔离开来的效果。**

使用iframe这个语言诞生以来就有的、用来隔离作用域和样式的元素相比，使用影子DOM有什么区别么？iframe，除了让人觉得恐怖外，它的设计意图实际上是为了将别的完全分离的HTML文档嵌入进来，也就是说从设计上来说，获取这些完全分离的文档（完全分离的上下文）中的DOM元素是不靠谱和极其麻烦的。另外可以试想一下用这种机制在页面中插入多个组件，你不得不为每一个iframe配备一个独立的url地址，不得不让你的HTML中充斥着几乎毫无意义的庞大的iframe标签。相比而言，用影子DOM实现组合边界的组件，使用和修改时就和普通的HTML元素一样直观。

唱一下反调，某些人可能认为组合化不应该发生在展现层（presentation layer）（这种做法可能不合你的口味），另外一些人可能认为无所谓。好吧，我们可以开始这么假设，假设你并没有采用影子DOM去实现组件的组合，但是紧接着，你为了实现组件的组合化，你在编写代码的时候不得不对组件的边界极其谨慎，或者你可以自己添加额外的抽象方法来避免边界问题。对比之下，影子DOM提供给你了一个平台级别的抽象来实现这个目的。

目前Chrome已经支持影子DOM，并且在将来有更多的其他浏览器会提供支持，同时，我们欣喜地看到很多人在现有的方法里寻找支持影子DOM的方法。例如，React在探索增加影子DOM的支持，并且打算尽快加上这项功能。Polymer已经支持影子DOM。Ember和Angular似乎也有在这方面探索的意思。

## 组件通信

那组件之间的通信呢？如果你正在研究解耦的、模块化的组件，那么下面有一些点。

**通过组件提供的API直接引用并不推荐（除非是一些特别简单的应用），因为这会导致对别的组件的某些版本产生了直接依赖。**如果它们的API变动巨大，你就不得不吃被动升级代码和不得不解决奔溃的苦果。所以，你应该采用传统的全局事件系统或组件内的事件系统。如果你要在两个没有父子关系的模块之间进行通信，事件+订阅的方式仍旧是一种流行的做法。实际上，React推荐这种方法，除非组件间有父子关系，这种情况下，只要通过传递[props](http://facebook.github.io/react/tips/communicate-between-components.html)就能实现。Angular采用服务（Services）的方式来通信，Polymer则有一系列选择：自定义事件、变动侦测器（change watcher）以及<core-signals>元素。

我们只能做这些了么？远远不是。**对于那些触发后就不管的事件，全局事件系统模型就已经能满足需求了，但是如果你需要含有状态性的事件（stateful events）或链接（chaining），这么做就不可取了。**当复杂度提升之后，你会发现这么做会使通信和流程控制变得混乱。虽然也许有很多方法来改进你的事件系统（例如函数反应式编程（functional reactive programming）），你也许会发现事件执行着巨量的代码。

**一个比全局事件系统更好的方式是通信顺序进程（CSP，Communicating Sequential Processes）**，它是一种用来描述并发系统中的通信的规范化的方法。CSP通道（CSP channels）可以在ClojureScript和Go语言中找到，并且在[core.async](https://github.com/clojure/core.async/)项目中规范化了。CSP可以处理那些需要信息传递的进程之间的协调配合，在通道中取数据和送数据的时候阻塞执行，从而能更轻松地传达复杂的异步流。它们所解决的问题，在某些时候，你为了实现相同效果，需要用到双工通道（duplex channels），或者需要依赖下面这种字串化的传统方法来实现：

<blockquote style="text-align:left">
thingNeedsPhoto { id: 001, uuid: “foo” }
thingPhoto { data: “../photo.png”, uuid: “foo” }
</blockquote>

以后再将两者匹配在一起。因为你现在是在一个有损耗、没有优化的事件通道基础上重新构建了函数调用，每当有因为书写错误导致的不匹配，调试就会变得异常痛苦。

![](https://d262ilb51hltx0.cloudfront.net/max/1677/1*tdy1UQP2PIXjE7GtxHXwRA.png)




* 本篇博客遵循[Creative Commons](http://creativecommons.org/)协议，转载请以链接形式标明本文地址
本文地址：[http://melon.github.io/blog/2014/12/16/translation-javascript-application-architecture-on-the-road-to-2015/](http://melon.github.io/blog/2014/12/16/translation-javascript-application-architecture-on-the-road-to-2015/)