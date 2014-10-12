title: "JSONP是如何工作的？"
date: 2014-08-08 14:05
categories:
- IT
tags:
- javascript
- jsonp
---
我对这个问题的探究来源于一个需求：

当访问某个页面的时候，需要向另外一个网站报告一下这次访问的信息。

其实发一个跨域的请求就能大致实现这个需求。我们发跨域的例子其实很常见，例如请求一个第三方的图片、引入一个第三方的样式文件、引入一个cdn上的js文件。然后，说到发送请求，在这个web2.0的时代，我们自然而然会想到Ajax请求。但是遗憾的是，考虑到安全问题，即所谓的[同源安全策略](http://en.wikipedia.org/wiki/Same-origin_policy)，用ajax请求一个第三方的地址是被浏览器所禁止的。然而天无绝人之路，有个叫JSONP的技术就是来解决这种问题的。

说道JSONP，可能多数人对它的知晓程度仅限于jQuery，jQuery提供了发送jsonp请求的方法。比如在使用$.ajax()方法的时候：

{% code lang:js %}
// Using YQL and JSONP
$.ajax({
    url: "http://query.yahooapis.com/v1/public/yql",

    // the name of the callback parameter, as specified by the YQL service
    jsonp: "callback",

    // tell jQuery we're expecting JSONP
    dataType: "jsonp",

    // tell YQL what we want and that we want JSON
    data: {
        q: "select title,abstract,url from search.news where query=\"cat\"",
        format: "json"
    },

    // work with the response
    success: function( response ) {
        console.log( response ); // server response
    }
});
{% endcode %}

jQuery将jsonp请求封装成类似ajax请求的样子，这样能让开发者在使用的时候更加方便，但是实际上，jsonp压根不是通过XMLHttpRequest来实现的。

一个简单的JSONP请求可以通过一下代码实现：

{% code lang:js %}
var callbackName = 'iAmTheCallback';
window[callbackName] = function (uuu, vvv, www) {
    // 对返回的数据做后续处理
}

var script = document.createElement('script');
script.src = 'http://melon.github.io/xxx/yyy?callback='+callbackName;
document.body.appendChild(script);

{% endcode %}

这是前端部分的代码，要想真正实现JSONP的功能，还需要后端的配合。针对上面这个例子，当前端请求这个script地址的时候，后端只要按以下内容响应就会有神奇的效果：

{% code lang:js %}
iAmTheCallback('abc', 'def', 'ghi');
{% endcode %}

说道这儿，很多人也许就恍然大悟了。JSONP名字听着高端，其实也不过如此嘛。

实际上，jQuery发JSONP请求时也是这么做的，去[这里](https://github.com/jquery/jquery/blob/master/src/ajax/script.js)可以窥见一斑。

- References:
    1. [So how does JSONP really work?](http://schock.net/articles/2013/02/05/how-jsonp-really-works-examples/)
    2. [Working with JSONP](http://learn.jquery.com/ajax/working-with-jsonp/)
    3. [jquery / src / ajax / script.js](https://github.com/jquery/jquery/blob/master/src/ajax/script.js)