title: 关于require的循环依赖问题
date: 2014-11-21 10:12:00
categories:
- IT
tags:
- javascript
- nodejs
- browserify
---
最近研究用browserify开发React项目的时候遇到了一个由require循环依赖(circular dependencies)引发的问题，两个循环依赖的模块的第二个模块中对第一个模块require之后得到的值是**undefined**。定位到这个问题之后，搜索相关资料，就发现了[引文1](http://aeflash.com/2014-03/a-year-with-browserify.html)这篇文章，作者在讲他在使用browserify一年里遇到的一些问题以及经验心得。文章中其中一个点讲的就是循环依赖，作者说了三种解决方法。

第一种方法是引入全局变量。这种方法明显是不推荐的，首先它会造成全局变量污染，其次在众多的依赖分析工具中，这个全局的模块也不能被展示出来。另外，我觉得如果是在像nodejs这样的非客户端的项目里，似乎这种方法压根就行不通。

第二种方法是延迟require(deferred require)。但是这种方法你需要保证第二个模块不能在当前js事件循环里就用到第一个模块，即只有当第二个模块并不会“立即”用到第一个模块的情形时延迟require的方法才有效，否则的话，第二个模块中对第一个模块的引用结果还会是**undefined**。

最后作者得到了他认为的最可行的方法

{% code lang:js %}
//app.js
//...
var controller = require("./controller.js")(app)
//...
module.exports = app;

//controller.js
//...
module.exports = function (app) {
  var controller = new Controller({
    // do something with `app`...
  });

  return controller
}
//...
{% endcode %}

其实是通过向依赖模块传送变量的方式来破解循环依赖的结。


- Reference:
	1. [A YEAR WITH BROWSERIFY](http://aeflash.com/2014-03/a-year-with-browserify.html)