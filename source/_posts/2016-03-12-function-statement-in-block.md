title: 关于在Javascript代码块中写函数申明
date: 2016-03-12 11:43:09
categories:
- IT
tags:
- javascript
- function
---
最近在看[ECMA-262-3 in detail. Chapter 5. Functions.](http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/)中对一个问题的剖析时突然回想起之前在工作开发中遇到的一个诡异的问题。问题代码大致如下：

{% code lang:js %}
vm.save = function () {

    if ($scope.form.$valid) {
        var diff = diffToHistory(vm.queryObj);

        function submit() {
            // ...
        }

        if (diff.length) {
            // ...
        } else {
            submit();
        }
    }
};
{% endcode %}

这段代码我在Chrome中测试的时候发现没有什么问题，然后在Safari下却报了一个ES5语法相关的错误。当时觉得特别纳闷，如果是明显的语法错误，为什么Chrome不报错呢？

看了上面那篇文章，才算明白了。总结一下就是，*对于某个特定的语法，不同浏览器对待的方式不一样*。

实际上，根据规范，*函数申明只能写在全局下或者是另一个函数块下*，而不能写在其它普通的代码块下，如if代码块、for代码块等，如果出现这种情况，应该视为语法错误。然而以前的（包括现在的）很多浏览器具体实现时并不认为这是错误，而是把它视为正确的语法。

上面说的Safari报错，至少说明那个版本的Safari对规范遵循地比较严格，将这种规范中定义为错误的情况如实地视为错误。

再举个例子

{% code lang:js %}
if (true) {

  function foo() {
    alert(0);
  }

} else {

  function foo() {
    alert(1);
  }

}

foo(); // 1 or 0 ? test in different implementations
{% endcode %}

这种情况下，因为规范并没有说明（它已经将这种认定为语法错误了啊！！），所以不同浏览器就可能会有各自不同的处理方式了，有的直接报错（如上面的Safari），有的会用第二个申明覆盖第一个，有的还会有其他处理方式。

因此，最为保险的写法就是按照规范来，不要把函数申明写到if代码块、for代码块等代码块下。

References:
- [http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/](http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/)
