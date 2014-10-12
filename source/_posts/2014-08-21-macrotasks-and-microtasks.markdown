title: "Macrotasks和Microtasks"
date: 2014-08-21 14:39
categories:
- IT
tags:
- nodejs
- javascript
- performance
---
从[Macrotasks and Microtasks](https://github.com/YuzuJS/setImmediate#macrotasks-and-microtasks)这个项目里得知Macrotasks和Microtasks的概念，想深入研究一下，发现网上相关资料极为有限。尽管如此，接下来我将列出能找到的一些重要的解释和我的一些推测。

根据一些相关的资料，event loop应该维护着两个队列(queue)，一个是task queue(也叫macrotask queue)，另一个就是microtask queue。这两个队列，对应的就是上面说到的macrotasks和microtasks。那这两个队列有什么区别呢？简单来说，microtask queue应该比task queue拥有更高的优先级。

先来看一张图，下面这张图是[Dart](https://www.dartlang.org/)的事件队列(event queue)的示意图
<br>
![Dart's event queues](https://www.dartlang.org/articles/event-loop/images/both-queues.png)
<br>
可以看到Dart的event loop维护了两个时间队列，鉴于event loop的原理都是相同的，并且根据[8.1.4 Event loops](http://www.whatwg.org/specs/web-apps/current-work/multipage/webappapis.html#event-loops)里的一些解释，我们大致能得出浏览器或者nodejs应该都是遵循上图中所展示的原理的。

从图中可以清楚地看到，每一次event loop都是先去microtask queue里看看是否有等待执行的任务，没有的时候才再去看task queue里等待的任务。也就是说，event loop会优先把所有microtasks执行完成之后，然后再会去执行macrotasks。

说到这，我们回顾一下在[setImmediate,setTimeout和process.nextTick的区别](/blog/2014/08/21/setimmediate-settimeout-nexttick/)里提到的各个函数的特点，然后将它们和这张图结合起来看，就会发现其实每个函数有他们的特点的本质是由这两个事件队列决定的。setImmediate, setTimeout, setInterval都是将事件回调送入task queue里去的，而process.nextTick，以及[Object.observe](http://www.html5rocks.com/en/tutorials/es7/observe/)和[MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)，都是和microtask queue相关的。

- References:
    1. [Macrotasks and Microtasks](https://github.com/YuzuJS/setImmediate#macrotasks-and-microtasks)
    2. [Dart’s event loop and queues](https://www.dartlang.org/articles/event-loop/#darts-event-loop-and-queues)
    3. [8.1.4 Event loops](http://www.whatwg.org/specs/web-apps/current-work/multipage/webappapis.html#event-loops)
    4. [Data-binding Revolutions with Object.observe()](http://www.html5rocks.com/en/tutorials/es7/observe/)
    5. [MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)
