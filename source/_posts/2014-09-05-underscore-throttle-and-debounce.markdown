title: "_.throttle()和_.debounce()"
date: 2014-09-05 18:57
categories:
- IT
tags:
- javascript
- performance
---
今天这篇文章要从页面中的滚动事件说起。近来视差网站、滚动到底自动加载的流行，使得window.onscroll这个滚动事件的应用也变得越来越多。但是onscroll事件的触发频率高的特点在很多时候成为了影响页面性能的一个比较大的因素。每次短距离的滚动都会触发很多次的onscroll事件回调，如果每个回调触发都完整执行一遍回调函数里的全部代码，势必会影响页面性能。所以在使用onscroll事件的时候，我们会常常用到一个hack。

{% code lang:js %}
var didScroll = false;

window.onscroll = function () {
    didScroll = true;
};

setInterval(function() {
    if(didScroll) {
        didScroll = false;
        // do some stuff
    }
}, 100);
{% endcode %}

引入一个外部变量didScroll，记录一个状态：页面是否滚动过，然后用setInterval函数定时地去读取这个变量，一旦发现didScroll变为true之后，就去执行相应的操作。运用setInterval就相当于限制了回调被执行的频率，避免过多地执行处在滚动的过渡过程中触发的回调事件，提高了执行效率。

但是通过这种方式来限制频率会引发一些问题，一个是全部变量的引入（虽然能通过简单的方式避免），另一个是setInterval存在于页面的整个生存周期中，因为大部分时间我们是没有进行滚动页面的操作的，让setInterval不停地去检查滚动状态，显得有些浪费。所以为了解决诸如此类的问题，underscore引入了一个叫_.throttle()的方法。

throttle这个词本身的含义就是“节流”，就是说希望在一定的时间内限制函数执行的次数。_.throttle()的用法

{% code lang:js %}
/*
*   @param function     未处理的原始函数
*   @param wait         在wait毫秒内最多执行一次函数
*   @param(optional) options    其它附加选项{leading: true/false, trailing: true/false}
*   @return             返回一个高阶函数
*/
_.throttle(function, wait, [options])
{% endcode %}

针对上面的例子，下面这么写代码就能达到“节流”的目的，使得滚动事件回调在100ms内最多执行一次

{% code lang:js %}
var throttled = _.throttle(updatePosition, 100);
$(window).scroll(throttled);
{% endcode %}

查看_.throttle()的源码之后可以发现，这个函数的主要思想就是，用闭包保存一些状态变量，不污染外部变量环境，用setTimeout来限制函数执行频率，并且避免了setInterval长期执行的问题。

然后再来看看\_.debounce()函数，它的主要作用是，当某个函数连续被触发时，限制这个函数只有在当最后一次触发延迟一段时间之后才真正被执行。\_.debounce()这个词是“去抖动”的意思，学过单片机的同学对“去抖动”这个词应该不陌生。抖动现象原本是指当按下按钮/按键的刚开始的那一极短的时间里，由于按钮/按键的机械特性，会发生抖动现象。从电路的角度来看，就会发现在那段短暂的时间里，电信号的波形会呈现锯齿波一样的波形。电子学上有一种来消除“去抖动”的做法就是，延迟一段时间，等信号稳定了之后，再去检测电信号情况。_.debounce()函数采用的也是这个思想，

{% code lang:js %}
/*
*   @param function     未处理的原始函数
*   @param wait         在wait毫秒内最多执行一次函数
*   @param(optional) immediate    true/false true表示首次执行高阶函数的时候就立即执行
*   @return             返回一个高阶函数
*/
_.debounce(function, wait, [immediate])
{% endcode %}

debounce最常被用在窗口大小变换的时候，能避免在窗口resize的时候频繁地调用回调函数，提高效率。

{% code lang:js %}
var lazyLayout = _.debounce(calculateLayout, 300);
$(window).resize(lazyLayout);
{% endcode %}

_.debounce()函数的思想也是闭包和setTimeout，具体源码大家可以自行去研究～

- References:
    1. [annotated source code of underscore](http://underscorejs.org/docs/underscore.html)
    2. [Underscore.js for easy performance wins](http://www.tivix.com/blog/using-underscorejs-for-easy-performance-wins/)