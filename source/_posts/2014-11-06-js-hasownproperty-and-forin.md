title: hasOwnProperty与for in
date: 2014-11-06 19:54:08
categories:
- IT
tags:
- javascript
- hasOwnProperty
- for in
---
其实在很早之前就知道hasOwnProperty和for in之间似乎是存在着某种关系，似乎也曾好几次看到网上很多的解释，但是总是很快就忘了它们之间的“渊源”。今天总算逮到一个机会让我能比较形象和深刻地感知到它们之间的联系了。

事情是源自将React从0.11.2版本升级到了0.12.0版本，0.12.0版本有了许多重大的改动，于是console当中报了很多warning。与今天讲的主题相关的warning是copyProperties和merge这俩模块被标为deprecated了，官方推荐用Object.assign模块来替代这两个即将废弃的模块。前两个模块被废弃的主要原因是在用for in遍历对象属性的时候没有加入hasOwnProperty函数来检查，**有没有用hasOwnProperty函数检查的区别在于是否将对象原型链上的属性列入考虑范围之内**。也就是说，**for in是遍历对象的所有enumerable的属性，包括原型链上，而hasOwnPropery函数能识别出对象的直接enumerable属性**。而官方之所以推荐Object.assign，是因为它是ES6规范中新引入的一个方法，用来代替现有的五花八门的对象属性复制函数的。由于Object.assign目前几乎没有浏览器支持，所以facebook写了个polyfill，其中就加入了hasOwnProperty的判断。

来个例子帮助理解hasOwnProperty和for in

{% code lang:js %}
function A () {
    this.a = 1;
    this.aa = 2;
}
A.prototype.ap = 3;

function B () {
    this.b = 4;
    this.bb = 5;
}
B.prototype = new A();
B.prototype.bp = 6;

var b = new B();

for (var key1 in b) {
    console.log(key1, b[key1]);
}
// b 4
// bb 5
// a 1
// aa 2
// bp 6
// ap 3

for (var key2 in b) {
    if (b.hasOwnProperty(key2)) {
        console.log(key2, b[key2]);
    }
}
// b 4
// bb 5
{% endcode %}

**EDIT:** for in对所有可列举(enumerable)属性进行遍历，像由Array、Object这些内置构造器构造出来的对象，继承了Object.prototype和String.prototype中的不可列举属性，比如String类型的indexOf()函数、Object的toString()函数，这些函数都不会被for in遍历。

- References:
    1.[Object.prototype.hasOwnProperty()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)
    2.[for...in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)
- Related:
    1. [javascript中Object初始化的一些概念厘清](/blog/2015/02/07/js-object-initializer-concept/)
