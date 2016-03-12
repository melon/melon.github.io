title: 关于Javascript的function
date: 2016-03-12 10:07:10
categories:
- IT
tags:
- javascript
- functions
---
三种function：

- 函数申明（Function Declaration）
- 函数表达式（Function Expression）
- 用函数构造器创建的函数（Function created via Function constructor）

## 函数申明

有以下特点：

- 必须要指定函数名
- 可以在全局或另一个函数内部定义（按照规范，在另一个函数内部时必须是在最外层的函数块(block)下定义，不能在其他块下定义，如if块）
- 在进入上下文阶段（entering the context stage）创建（执行一个函数有两个阶段：1.进入上下文阶段，2.代码执行阶段）
- 会影响变量对象（variable object）

## 函数表达式

有以下特点：

- 处于表达式的位置（js解析器认为是表达式的各种情况下的位置）
- 函数名可有可无
- 对变量对象没有影响
- 在代码执行阶段（code execution stage）创建

## 用函数构造器创建的函数

有以下特点：

- [[Scope]]链上只有global对象
- 阻断了一些优化途径（[Equated Grammar Productions](http://bclary.com/2004/11/07/#a-13.1.1)、[Joined Objects](http://bclary.com/2004/11/07/#a-13.1.2)）

{% code lang:js %}
var x = 10;

function foo() {

  var x = 20;
  var y = 30;

  var bar = new Function('alert(x); alert(y);');

  bar(); // 10, "y" is not defined

}
{% endcode %}

上面用Function定义的函数，它的原型链上只有window对象

{% code lang:js %}
var a = [];

for (var k = 0; k < 100; k++) {
  a[k] = function () {}; // possibly, joined objects are used
}
{% endcode %}

如果浏览器有优化的话，上面a中每个元素都会共同使用一个函数，大大减少了函数消耗（但每个函数自身的数据还是独立的，比如如果有闭包的时候），对比之下，下面用Function创建的函数则会给每个元素都创建一个完全独立的函数。

{% code lang:js %}
var a = [];

for (var k = 0; k < 100; k++) {
  a[k] = Function(''); // always 100 different funcitons
}
{% endcode %}


References:
- [http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/](http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/)
- [http://www.swordair.com/blog/2011/10/714/](http://www.swordair.com/blog/2011/10/714/)
