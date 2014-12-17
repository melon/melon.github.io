title: 【翻】2015年JavaScript应用架构趋势
date: 2014-12-16 12:00:47
categories:
- Translation
tags:
- javascript
- architecture
---
翻译一篇最近被很多前端业内人士转发的文章，主要是学习一下当前前端趋势中的一些概念，同时也顺便分享一下好文。正好最近也在研究React和Browserify的相关东西，多了解下业界趋势也是很有益的。

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

我们只能做这些了么？远远不是。**对于那些触发后就不管的事件，全局事件系统模型就已经能满足需求了，但是如果你需要含有状态性的事件（stateful events）或链接（chaining），这么做就不可取了。**当复杂度提升之后，你会发现这么做会使通信和流程控制变得混乱。虽然也许有很多方法来改进你的事件系统（例如函数响应式编程（functional reactive programming）），你也许会发现事件执行着巨量的代码。

**一个比全局事件系统更好的方式是通信顺序进程（CSP，Communicating Sequential Processes）**，它是一种用来描述并发系统中的通信的规范化的方法。CSP通道（CSP channels）可以在ClojureScript和Go语言中找到，并且在[core.async](https://github.com/clojure/core.async/)项目中规范化了。CSP可以处理那些需要信息传递的进程之间的协调配合，在通道中取数据和送数据的时候阻塞执行，从而能更轻松地传达复杂的异步流。它们所解决的问题，在某些时候，你为了实现相同效果，需要用到双工通道（duplex channels），或者需要依赖下面这种字串化的传统方法来实现：

<blockquote style="text-align:left">
thingNeedsPhoto { id: 001, uuid: “foo” }
thingPhoto { data: “../photo.png”, uuid: “foo” }
</blockquote>

以后再将两者匹配在一起。因为你现在是在一个有损耗、没有优化的事件通道基础上重新构建了函数调用，每当有因为书写错误导致的不匹配，调试就会变得异常痛苦。

![](https://d262ilb51hltx0.cloudfront.net/max/1677/1*tdy1UQP2PIXjE7GtxHXwRA.png)

**CSP的机制提供了一种抽象，使得系统中的一部分功能能控制另一部分功能，有了这样的基础之后，构建并发的协作机制就变得更容易了。**实际上CSP给你做了一些相对底层的架构。在函数响应式编程中，信号代表了随着时间不断变化的值。并且信号它有一个基于推送的接口，所以它是响应式的。函数响应式编程提供了一种纯粹的功能性接口，所以你无需手动触发事件，但是你能知道控制流结构，从而你可以根据基础信号定义自己的变换规则。函数响应式编程中的信号就可以用CSP通道来实现。

[James Long](http://jlongster.com/Taming-the-Asynchronous-Beast-with-CSP-in-JavaScript)和[Tom Ashworth](http://phuu.net/2014/08/31/csp-and-transducers.html)有几篇关于CSP和转换器（Transducers，组合式算法转换器）的干货文章，如果你想要的远不止是全局事件系统，这几篇文章值得一看。

另外，你也有必要看看[js-csp](https://github.com/ubolonton/js-csp)这个项目，这个JavaScript项目提供了和ClojureScript的cor.async项目相似的功能，它采用了Generators（yield语句），而不是macros来实现了控制反转（IOC, Inversion-Of-Control）封装。

## ES6和Browserify

我之前写过有关大型系统在利用了解耦（decoupling）的优势和采用了JavaScript模块之后获益颇多的文章，我认为我们现在所面临的形势比几年前好多了，我们无需再仅仅局限于AMD,RequireJS以及模块模式（Module pattern）这些技术上了。

我们应该感谢围绕着Browserify（整个npm社区的模块都是Browserify兼容的）以及其他大量的转编译友好（transpilation-friendly）的ES6功能所发展起来的越来越多越来越可靠的小工具，这些小工具在ES6的Modules功能完全降临到各大浏览器之前是非常实用的。

![@thlorenz和@domenic的es6ify使得在使用ES5和ES6功能（还能支持source map）的时候的工作流程变得非常优雅](https://d262ilb51hltx0.cloudfront.net/max/1408/1*tNh8NTBrl74k06Id07Wggw.png)

**很多情况下我们都早已不在写代码的时候加入编译（build）步骤了，但是现在如果因为别人这么做就鄙视人家，这样的想法才是过时的。**而且很多JS库的作者都乐意在他们的编译前的代码中加入ES6的功能。

**ES6的Modules功能解决了大量的我们在依赖和部署使遇到的问题，让我们在构建模块时能用明确的exports，按名称导入模块并保持模块名之间相互分离。**ES6的Modules功能其实相当于兼具了AMD的异步特点（浏览器需要）和CommonJS的清晰，同时能用更优雅的方式处理循环依赖问题。

ES6模块功能时的依赖都是静态的，所以我们能得到可分析的依赖关系图（adpendency graph）,这非常有用。另外，ES6的模块功能相比CommonJS使用起来更加简洁（虽然Browserify工作流已经使模块功能使用起来非常舒适了）。为了使浏览器环境支持CommonJS，你要自己去封装模块或者通过XHR引入进来，你要自己去封装自己执行。尽管这么做了确实能然浏览器支持，但是ES6的模块还有个好处是能优雅地实现同时支持网页端和服务器端，因为它一开始就是从底层开始为同时支持客户端和服务器端而设计的。

在原声客户端这边，我也很兴奋地看到对ES6的支持正在不断地被V8,Chakra,SpiderMonkey和JSC等引擎尝试。也许最让我惊讶的是IE Tech Preview对[ES6兼容性表](http://kangax.github.io/compat-table/es6/)中的所有44个feature的支持达到了32个，是表中支持度最高的。

![IE Tech Preview已经支持ES6的Classes,for...of,Maps,Sets,typed arrays,Array.prototype方法等其他feature](https://d262ilb51hltx0.cloudfront.net/max/1024/1*mWPAXNkZmc5gjGU-rKNung.png)

如果你错过了之前我写的V8进度更新，下面简单提几点

**含有嵌入语法的模板字串/字面量（Template Strings/Literals），能使字串插入（string interpolation）更加容易：**

![](https://d262ilb51hltx0.cloudfront.net/max/1260/1*_WC8llYhsfJsfvAAmDAKcg.png)

**对象字面量。简写的属性和方法可以帮助你少打字：**

![](https://d262ilb51hltx0.cloudfront.net/max/877/1*tQT637o53hR239FRXkwJvg.png)

**ES6的类（Classes）为现行的对象和原型（prototypes）提供了语法糖：**

![](https://d262ilb51hltx0.cloudfront.net/max/919/1*HXf8FBnCOaKtURSu6a0oag.png)

不管是ES6的feature还是CommonJS的模块，我们都有足够的方法来使我们的项目通过强组合（strong compositions）来支持它们，无论是客户端还是服务器端，同构的（isomorphic）或者是其他的。这着实让人觉得惊奇。不过别理解错了，为了让前端的生态系统更加成熟我们还有很多路要走，但是毋庸置疑的是组合化的趋势在目前的前端非常强烈。

附注：我们已经谈了Web Components，[HMTL Imports](http://www.html5rocks.com/en/tutorials/webcomponents/imports/)也是值得谈的一个话题。JavaScript模块可能并不总是最好的针对组件和组件相应的模板的容器形式（container format），很多人仍旧在使用额外的工具去加载和解析组件。

另外我认为作为JS开发者，对脚本我们已经有了从某种程度上讲较为成熟的工具生态系统，但是如果回到HTML上，我们就不得不重写工具来实现HTML的依赖机制，这总让人觉得有点落后。我看到这个问题部分已经被解决了，利用像[Vulcanize](https://www.polymer-project.org/articles/concatenating-web-components.html)（用来缕清载入文件之间的依赖关系）这样的工具来解决，我希望的是这个问题在HTTP2全面普及的时候能完全消失。

我个人对多少地方该用纯脚本，又有多少地方该用import比较纠结，当我们开始看ES6的模块的时候这两者的界限又该划在哪里我也比较迷惑。但即便如此，我认为HTML的import对资源加载是个很好的方式，但它在加载脚本的时候不会阻塞解析器（parser）（但是它们仍会阻塞load事件）。但我仍然很希望我们能在将来看到import，模块以及两者的互相操作的比例能发生变化。

## 离线问题

**如果我们的应用在离线的时候不能工作，那么我们其实就没有提供一种好的手机页面体验。**

在以前，解决这个问题是需要面对很多根本性的挑战，但现在情况有些好转。今年，web平台的API不断地进化，给我们提供了更好的基本类型（primitives），其中近期最有趣的一个就是[Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker_API/Using_Service_Workers)。Service Workers是一个可以让网站在离线的时候还能工作的API，它通过劫取网络请求，然后按照事先编好的程序告诉浏览器这种情况下应该如何应对这些请求。

这其实是AppCache本应该做，只不过AppCache的控制程度不对。用Service Workers可以缓存内容，修改伺服的内容和把网络仅仅视为一种功能增强。想要了解更多关于Service Worker的东西，可以去看Matt Gaunt的[Service Worker primer](http://www.html5rocks.com/en/tutorials/service-worker/introduction/)或Jake Archibald的[offline patterns](http://jakearchibald.com/2014/offline-cookbook/)。

在2015年，我希望看到我们有越来越多的构建在Service Worker基础之上的路由和状态管理的库。对任何你浏览过的路径的第一等级的离线和同步支持的需求会特别庞大，特别是如果开发者能免费得到它们的情况下。

Service Workers通过在重复访问时来缓存视图（view）和资源（asset），能显著提高性能。并且Service Workers只是一个基本的API，请求控制只是一些列由此带来的新功能中的一个，其他的功能像推送通知（Push Notifications）和后台同步（Background Synchronization）等也能因此实现。

想了解当前Chrome是如何使用推送通知的，可以阅读Matt Gaunt的[Push Notifications & Service Worker](https://gauntface.com/blog/2014/12/15/push-notifications-service-worker)。

## 组件API和“正面模式”

有人或许会说我之前在文章中提过的“正面模式”在今天仍旧是可行的，特别是如果你注意不让组件里的实现细节不会泄露到全局API。如果你能为你的组件定义一个简洁、健壮的接口，使用者就可以继续利用这样的组件，而无需担心内部实现细节。这些都可以在任何时候花最少的代价来更改。

我还要提一下的是这是一种比较好的模型，框架和库作者在开发公开组件应该遵循这样的模型。虽然这完全没有和Web Components绑在一起，我还是很高兴看到尽管Polymer里的paper-*元素不断地进化，但是内部的变化对对外的公共API造成的影响非常小。这对使用者来说再好不过了。别尝试打破“不要让用户感到惊讶”这一规则，也就是你的组件的用户不应该被你的更改所影响到。坚持这么做就能既让你的用户更高兴，也能让你的团队更高兴。

## 不可变和持久化的数据结构

在我之前的关于大规模JS应用的文章里，我提到过不可变的和持久化的数据结构（immutable & persistent data structures）。如果你听过类似[immutable-js](https://github.com/facebook/immutable-js)或[Mori](http://swannodette.github.io/mori/)的库，但是却不清楚它们的价值在哪，那么你可能需要试试去看一下入门的文章。

不可变数据结构是一种一旦被创建后就不能再被更改的数据结构，这意味着有效地更改数据的方式就是创建一个数据副本。持久化的数据结构是一种在数据改变是能保持数据更改前版本的数据结构。像这样的数据结构是不可变的（它们的状态一旦被创建就无法修改）。更改并不会在原来的数据结构中发生，而是生成一个新的更新后的数据结构。所有指向原始数据结构的东西都会得到保证这个结构永远不会发生变化。

<blockquote style="text-align:left">
持久化的数据结构的真正好处在于引用相等（referential equality），因此当比较内存中的地址时会显得特别清楚，你不仅拥有相同的对象，你还拥有相同的数据。 ---Pascal Hartig, Twitter
</blockquote>

让我们尝试将不可变数据结构运用到Todo app中。假设在我们的应用中有一个普通的JS数组来表示Todo的条目，在内存中有一个引用会对应这个数组，并且这个数据有特定的值。然后一个用户加了一条新的Todo条目，改变了这个数组。这个数组现在被修改了。在JavaScript中，内存中这个数组数组的引用地址并未发生改变，只是引用地址指向的值发生了变化。

我们知道，如果数组中某个元素变了，我们需要对数组中的每个元素进行一个对比操作，这是一项非常耗费资源的操作。现在让我们假象一下我们所用的并不是vanilla数组，而是不可变的数组。这种数组可以通过Facebook的immutable-js或者Mori来创建。这个时候当更改了数组中的一个元素之后，我们得到了一个新的数组，这个新的数组在内存中会有一个新的引用地址。

假设说我们回头检查发现数组的引用地址是一样的，那么数组肯定没有发生变化。数组中的值肯定是一样的。这样，我们就能做所有事情了，像快速高效地比较俩数组是否相等。之所以高效是因为你只是检查了数组的引用，而没有去一个一个地对比Todos数组中的每个元素。



（时间仓促，能力有限，翻译错误在所难免，如有问题，请联系[melon](https://github.com/melon)或在下方评论）

* 本篇博客遵循[Creative Commons](http://creativecommons.org/)协议，转载请以链接形式标明本文地址
本文地址：[http://melon.github.io/blog/2014/12/16/translation-javascript-application-architecture-on-the-road-to-2015/](http://melon.github.io/blog/2014/12/16/translation-javascript-application-architecture-on-the-road-to-2015/)