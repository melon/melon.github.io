title: "setInterval的堆叠问题"
date: 2014-09-03 22:27
categories:
- IT
tags:
- javascript
---
最近被问及一个关于setInterval的问题：

程序中原有一setInterval定时器每隔一小段时间，比如100ms执行，然后突然来了个Cpu-bound的任务，将线程给堵塞了，比如堵了1000ms，期间setInterval被触发了多次，问该任务执行完之后，setInterval接下来会怎么操作。

然后就引发了两种意见：

1.在堵塞期间触发后入队等待的回调函数会一次不落、一个紧接着一个执行完。

2.在堵塞期间对于一个setInterval id（setInterval设置之后都会返回一个id），最多只能有一个等待执行的回调，如果有多次，后续触发的回调都会被丢弃，只留第一个等待的回调。

到底那种情况是事实？我们下面就来分析。

说到javascript的定时器，就不能不提[John Resig](http://ejohn.org/about/)(jQuery的发明者)的这篇关于[javascript定时器](http://ejohn.org/blog/how-javascript-timers-work/)的文章。

文中有一张非常棒的图
![javascript wall clock](http://ejohn.org/files/Timers.png)

注意到图中有“Dropped”字样，说的正是上文提到的问题。在30ms的时候，上次触发(fire)的setInterval回调还没来得及执行（因为此刻Timer的回调正在执行），又紧接着第二次触发了setInterval回调，这个时候并不是说这两个回调都会入队等待，而是第二个回调会被丢弃掉。我们可以设想一下，如果阻塞的时间足够长，可能会有更多次的回调函数被丢弃的情况。所以其实上面第2种意见应该是更正确一些，第1种是肯定错误的。

为了进一步证明第2种意见，我做了个实验。我写了一段如下的代码

{% code lang:js %}
setInterval(function(){
    console.log(process.hrtime())
}, 100);

var x=0;
setTimeout(function () {
    console.log('*********************************')
    while (x++ < 100001000) {

    }
}, 1000);
{% endcode %}

用nodejs执行上面的程序，得到如下结果

{% code lang:js %}
...
[ 893372, 222583483 ]
[ 893372, 323126588 ]
[ 893372, 424112082 ]
[ 893372, 524502223 ]
[ 893372, 624844093 ]
[ 893372, 725233762 ]
[ 893372, 825749011 ]
*********************************
[ 893376, 825313230 ]  // <--- 阻塞结束后首次执行回调
[ 893376, 925722228 ]  // <--- 与上次相隔约100ms
[ 893377, 27397835 ]   // <--- 与上次相隔约100ms
[ 893377, 127775077 ]
[ 893377, 228090159 ]
[ 893377, 328551931 ]
[ 893377, 429070043 ]
[ 893377, 529448029 ]
[ 893377, 629963588 ]
...
{% endcode %}

可以看到（在星号分割线附近），程序被阻塞了4s，在这4s中，setInterval回调原本应该被触发很多次，但是由于阻塞的原因未触发，但这些原本应该触发的回调在阻塞结束后是否还有机会执行呢，答案是否定的，看看星号分割线下的第一行和第二行，秒部分一样，纳秒部分是825313230和925722228，相差了约100ms，如果原本应该触发的回调在阻塞结束后要是连续不断地执行的话，这两者相差的时间不应该有100ms这么长，能合理解释的就是这些回调函数被丢弃了。

用浏览器执行上述类似代码，也能得到类似的结果

{% code lang:js %}
setInterval(function(){
    console.log(performance.now())
}, 100);

var x=0;
setTimeout(function () {
    console.log('*********************************')
    while (x++ < 5000100) {

    }
}, 1000);
{% endcode %}

{% code lang:js %}
...
44630.204000010395
44731.06900001039
44831.93000001035
44932.724000010356
45033.704000010344
*********************************
47831.638000010345 // <--- 阻塞结束后首次执行回调
47932.16400001034  // <--- 与上次相隔约100ms
48033.02100001036  // <--- 与上次相隔约100ms
48134.03900001037
48234.835000010375
...
{% endcode %}

但是，值得一提的是，两个setInterval紧贴着执行的可能是存在的，上面那张图里就有这种情况。

“Intervals may execute back-to-back with no delay if they take long enough to execute (longer than the specified delay)”

John Resig的文章里有这么一句话，它的意思是，当setInterval的回调执行时间（比如说5ms）比延迟时间（比如3ms）长的时候，就会出现setInterval的回调一个挨着一个连续不断执行的情况。

- References:
    1. [How JavaScript Timers Work](http://ejohn.org/blog/how-javascript-timers-work/)
