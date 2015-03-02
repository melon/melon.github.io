title: "一个牵扯到apply和bind的代码小片段"
date: 2014-09-30 15:36
categories:
- IT
tags:
- frontend
- javascript
---
今天在研读express-session代码的[这段代码](https://github.com/expressjs/session/blob/master/index.js)的时候，看到了一段代码。

{% code lang:js %}
var defer = typeof setImmediate === 'function'
  ? setImmediate
  : function(fn){ process.nextTick(fn.bind.apply(fn, arguments)) }
{% endcode %}

这是一段为了使setImmediate函数兼容支持Nodejs 0.8以下版本而写的polyfill。看到这种写法，我不由地想为什么这么写。

如果不用apply，这么写
{% code lang:js %}
var defer = typeof setImmediate === 'function'
  ? setImmediate
  : function(fn){ process.nextTick(fn.bind(arguments)) }
{% endcode %}
这样写是有问题的，arguments是个类数组，直接以参数形式传入得到的结果不是我们期望的。

既然bind的本意就是为了改变函数执行时的this，那么apply本身就能实现
{% code lang:js %}
var defer = typeof setImmediate === 'function'
  ? setImmediate
  : function(fn){
        var args = Array.prototype.slice.call(arguments, 1);
        process.nextTick(function () {
            fn.apply(fn, args);
        });
    }
{% endcode %}
对比之后，显然最初的那段实现是最简洁的～
