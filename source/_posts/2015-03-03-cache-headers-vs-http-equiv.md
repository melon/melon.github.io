title: 处理缓存响应头与http-equiv的区别
date: 2015-03-03 22:28:09
categories:
- IT
tags:
- cache
- performance
- http
- html
---
有时候在html的头部会看到这样的meta标签，这些meta标签信息是用来给浏览器当做当前的html文件（？）应该怎样来缓存的依据。

{% code lang:html %}
<meta http-equiv="cache-control" content="no-cache" />
<meta http-equiv="expires" content="Tue, 01 Jan 1980 1:00:00 GMT" />
<meta http-equiv="pragma" content="no-cache" />   <!-- 在新的浏览器实现中，pragma:no-cache会被当作cache-control:no-cache处理 -->
{% endcode %}

那么它与响应头里设置相应的头字段有什么不同？

其实，单对浏览器来说，这两者的效果应该是相同的。不同之处在这两者对CDN等中间代理的影响不同，html中的meta标签的http-equiv属性只影响浏览器，而响应头中的相关属性除了影响浏览器，也可能会影响到这些中间代理处理缓存的方式。

- References:
  1. [Useful HTML Meta Tags](http://www.i18nguy.com/markup/metatags.html)
  2. [A Beginner's Guide to HTTP Cache Headers](http://www.mobify.com/blog/beginners-guide-to-http-cache-headers/)
  3. [how should cache control be set in the requests](http://webmasters.stackexchange.com/a/34588)
