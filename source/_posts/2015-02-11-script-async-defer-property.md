title: script标签的async属性与defer属性
date: 2015-02-11 18:28:38
categories:
- IT
- Notes
tags:
- html
- tag
- javascript
- js
- script tag
---

![](http://peter.sh/wp-content/uploads/2010/09/execution2.jpg)

async不支持IE9以及IE9以下浏览器

defer虽然支持到IE6，但是无法保证script的执行顺序，所以如果要支持IE，目前意义可能不大

对于一些完全和页面逻辑无关的script脚本推荐用这两个属性，如统计代码

- References:
	1. [Asynchronous and deferred JavaScript execution explained](http://peter.sh/experiments/asynchronous-and-deferred-javascript-execution-explained/)
	2. [script[defer] doesn't work in IE<=9](https://github.com/h5bp/lazyweb-requests/issues/42)