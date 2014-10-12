title: "setImmediate,setTimeout和process.nextTick的区别"
date: 2014-08-21 00:11
categories:
- IT
tags:
- nodejs
- javascript
- performance
---
之前已经在无数地方看到过关于这三者的区别，今天详细研究了一下，下面会总结一些重点。

setTimeout对于前端开发工程师来说已经很熟悉了，无须过多解释。但是，下面这种
{% code lang:js %}
setTimeout(function () {
    // blaabla
}, 0);
{% endcode %}
用的情况应该不多，但凡是这么用的，将延迟时间设为0，我们所期望的肯定是希望setTimeout的第一个参数-回调函数能“尽快”执行。实际上，如果在浏览器里，上面这个回调函数不会在0ms后执行，而是最快也要在一个“时间分辨率”(time resolution)之后执行。这个时间分辨率，对不同浏览器来说可能不同，有15.6ms的，也有4ms的，这个时间分辨率的设置，据说是为了延长笔记本电池的寿命。。。而在nodejs里，应该没有时间分辨率的概念，但即便如此，将延迟时间设为0的做法也没有后面提到的setImmediate更加高效。

setImmediate从字面上理解是立即执行的意思，这应该是相对于setTimeout延迟一段时间执行代码来说的，但这里的立即，可能并非是我们所想象的那样的。这里的立即，必然不是在执行到这个函数时，回调函数立即执行，倘若如此，便与同步执行代码无异了。实际上，setImmediate的回调函数的执行时刻，是在当前同步代码执行完，再等待当前时刻已经在event队列里等待执行的回调函数执行完后，并且如前面所说，这个时刻肯定会保证在setTimeout和setInterval之前（如果刚好大家都挤一块儿了。。）。

process.nextTick也有些立即执行的意味，但它比setImmediate还要“快”，它会将回调函数置于event队列的最顶端（意思是它马上就能出列执行了），所以，当当前同步代码一执行完，nextTick的回调就几乎能立即执行了，约等于同步执行了。

<strong>Edit(2014-08-21):</strong>
其实[深入研究](/blog/2014/08/21/macrotasks-and-microtasks/)后发现，在当时，我看到的可能只是一个表象，虽然能解释得通，但其实是因为有两个event队列。

setTimeout(function(){}, 0)和setImmediate(function(){})的性能对比在[这个网站](http://jphpsf.github.io/setImmediate-shim-demo/)有个非常直观的对比。

Github上有个叫[YuzuJS/setImmediate](https://github.com/YuzuJS/setImmediate)的项目，目的就是为了实现setImmediate函数在大部分浏览器上的模拟。

另外，基于setImmediate的特点，它有一个非常有用的用途：可以用setImmediate来将耗时较长的CPU密集型(CPU-bound)的任务拆分成一块块子任务，在每个子任务执行完之后，给那些在等待中的异步回调“让行”，从而避免长时间阻塞线程。

- References:
    1. [Script yielding with setImmediate](http://www.nczonline.net/blog/2011/09/19/script-yielding-with-setimmediate/)
    2. [YuzuJS/setImmediate](https://github.com/YuzuJS/setImmediate)
    3. [nodejs API - setImmediate(callback, [arg], [...])](http://nodejs.org/api/timers.html#timers_setimmediate_callback_arg)
    4. [nodejs API - process.nextTick(callback)](http://nodejs.org/api/process.html#process_process_nexttick_callback)
    5. [setImmediate vs. nextTick](http://stackoverflow.com/questions/15349733/setimmediate-vs-nexttick)
    6. [setImmediate API demo](http://jphpsf.github.io/setImmediate-shim-demo/)
