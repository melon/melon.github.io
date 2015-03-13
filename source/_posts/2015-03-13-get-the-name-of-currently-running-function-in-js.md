title: 关于从函数内部获取当前执行函数的函数名
date: 2015-03-13 10:41:30
categories:
- IT
tags:
- javascript
- arguments.callee
---
对，答案就是用arguments的callee属性。

### 具体怎么做

argument.callee.toString()返回的时整个函数内容的字符串，所以需要截取

{% code lang:js %}
function DisplayMyName() {
  var myName = arguments.callee.toString();
  myName = myName.substr('function '.length);
  myName = myName.substr(0, myName.indexOf('('));

  alert(myName);
}
{% endcode %}

### arguments.caller是什么

<!--more-->

已废弃。是的，已废弃！不要再管它了，如果非得管，自己查去。

对了，arguments.callee.caller或者说Function.caller都不建议使用。

### arguments.callee也不推荐使用了

什么？？

是的，不推荐使用了。虽然现在可能还可以使用，但是已经被逐渐抛弃了。ES5的严格模式已经禁止使用这个了。

那有这种在函数内部希望引用当前执行函数的需求怎么办？

举个例子，一下的情况需要用到arguments.callee

{% code lang:js %}
[1,2,3,4,5].map(function (n) {
    return !(n > 1) ? 1 : /* what goes here? */ (n - 1) * n;
});
{% endcode %}

但是不能用了，该怎么办？

一种方法是将map的回调函数单独声明出来

{% code lang:js %}
function factorial (n) {
    return !(n > 1) ? 1 : factorial(n - 1) * n;
}

[1,2,3,4,5].map(factorial);
{% endcode %}

另外一种是给函数表达式一个名字

{% code lang:js %}
[1,2,3,4,5].map(function factorial (n) {
    return !(n > 1) ? 1 : factorial(n-1)*n;
});
{% endcode %}

这第二种有几个好处

- 不会在外部作用域创造变量（[ie8一下除外](http://kangax.github.io/nfe/#example_1_function_expression_identifier_leaks_into_an_enclosing_scope)）
- 能实现arguments.callee的功能，但比用arguments对象效率高


- References:
  1. [arguments.caller](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/caller)
  2. [arguments.callee](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/callee)
  3. [Can I get the name of the currently running function in JavaScript?](http://stackoverflow.com/questions/1013239/can-i-get-the-name-of-the-currently-running-function-in-javascript)
