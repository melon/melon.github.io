title: "用createDocumentFragment来提升网页渲染速度"
date: 2014-08-18 23:53
categories:
- IT
tags:
- dom
- performance
- frontend
---
说到document.createDocumentFragment, 我们肯定需要首先了解什么是DocumentFragment. DocumentFragment从字面上理解，就是Document的一个片段，也就是一个完整Document的一部分，用专业术语来说就是轻量级(lightweight)的Document对象。

DocumentFragment并不是Dom Tree的一部分，它是游离在Dom树外的一个结点。它通常的应用场景是，创建一个DocumentFragment，在它内部append一些元素，然后再将它append到Dom树中去。

为什么不直接将元素append到Dom树中去？

我们先来看个例子。假设我们希望给某个元素append 100个div.
{% code lang:js %}
var div;
var element = document.getElementById('container');
for (var i = 0; i < 100; i++) {
    div = document.createElement('div');
    div.innerHTML = 'Melon';
    element.appendChild(div);
}
{% endcode %}
然后我们要提出一个reflow的概念。reflow是指浏览器的这样一种行为：为了重新渲染部分或整个页面，需要对页面中的元素重新计算位置和形状。显然，relow的次数应当尽量减少。而上面的例子，每一次element在appendChild的时候，都触发了一次reflow。这样的做法显然不是最优的。这时，如果用上DocumentFragment，就会有神奇的效果。
{% code lang:js %}
var div;
var element = document.getElementById('container');
var fragment = document.createDocumentFragment();
for (var i = 0; i < 100; i++) {
    div = document.createElement('div');
    div.innerHTML = 'Melon';
    fragment.appendChild(div);
}
element.appendChild(fragment);
{% endcode %}
上面的例子，只触发了一次reflow，当div的数量比较多的时候，两个例子速度上应该会有明显的差别。

为什么用了DocumentFragment就会有这样的效果？

原因在于DocumentFragment是只存在于内存之中的，而并非是Dom树的一部分，所以往DocumentFragment上append元素并无触发网页reflow的必要（因为元素此刻压根没有添加到实际的Dom树中去）。

DocumentFragment的兼容性也不错，IE6都能支持，所以能用的话就没有不用的理由了～

- References:
  1. [document.createDocumentFragment](https://developer.mozilla.org/en-US/docs/Web/API/document.createDocumentFragment)
  2. [Interface DocumentFragment](http://www.w3.org/TR/DOM-Level-2-Core/core.html#ID-B63ED1A3)
  3. [Minimizing browser reflow](https://developers.google.com/speed/articles/reflow)