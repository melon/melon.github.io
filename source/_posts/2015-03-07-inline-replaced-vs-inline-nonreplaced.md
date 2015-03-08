title: inline replaced element和inline non-replaced element
date: 2015-03-07 13:45:50
categories:
- IT
tags:
- css
- w3c
---
一直以来，由inline non-replaced elements造成的一些不那么直觉化的css效果，常常让我疑惑，只知其然，不知其所以然，甚至有时候记混了连只知其然都做不到。这类问题，我觉得有必要从规范角度来做下剖析，利于记忆之外，还可以对css有更深入的了解。

首先得解释一下replaced element和non-replaced element的区别

css2.1 规范里对replaced element是这样定义的

An element whose content is outside the scope of the CSS formatting model, such as an image, embedded document, or applet. For example, the content of the HTML IMG element is often replaced by the image that its "src" attribute designates. Replaced elements often have intrinsic dimensions: an intrinsic width, an intrinsic height, and an intrinsic ratio. For example, a bitmap image has an intrinsic width and an intrinsic height specified in absolute units (from which the intrinsic ratio can obviously be determined). On the other hand, other documents may not have any intrinsic dimensions (for example, a blank HTML document).

User agents may consider a replaced element to not have any intrinsic dimensions if it is believed that those dimensions could leak sensitive information to a third party. For example, if an HTML document changed intrinsic size depending on the user's bank balance, then the UA might want to act as if that resource had no intrinsic dimensions.

The content of replaced elements is not considered in the CSS rendering model.

简单来说，就是replaced element指的是那些展现方式已经脱离css的束缚（或者说独立于css）的元素，最常见的这类元素就是&lt;img&gt;元素了，图片有它内在的长宽或长宽比率。通常这类元素展现的并不是该元素标签所包含的子元素内容，而是相当于完全被另一个东西给“替换”了一样。常见的replaced element有&lt;img&gt;,&lt;object&gt;,&lt;video&gt;以及表单类&lt;textarea&gt;,&lt;input&gt;，另外&lt;audio&gt;,&lt;canvas&gt;在某些情况下也属于replaced element，还有，用css的content属性添加的对象也是一种anonymous replaced element。

而non-replaced element就是replaced element的反面，一般大部分标签都是non-replaced element。

那为什么会有这两种分类？

<!--more-->

我想其中一个很重要的原因也许就是从css角度讲它们两类的展现规则会有些不同，所以需要区分，以便分别制定规则。

我觉得block-level的情况下，这两类元素的展现方式可能是挺直觉化的，所以并不是难点。倒是在inline的情况下，这两类会产生一些非直觉化的效果，所以对这种情况的剖析才是本文的重点。

对于inline non-replaced element：
* height不会有任何视觉上的效果，height应当由字体决定，具体怎么决定，规范没有说明
* 垂直方向上的margin不会有任何视觉效果
* 垂直方向上的padding和border似乎是可以设置的，padding还会影响到border的展现，但是这两个和line-box的line-height是互不影响的，所以说视觉上可能会发生不同行的padding和border相互重叠交错的情况

对于inline replaced element，展现是相对比较符合直觉的：
* margin-top和margin-bottom会起作用，当它们的值为auto时，所使用的值是0
* 如果height和width都是auto，元素有固有高度，则使用该固有高度
* 如果height是auto，width是指定值，元素有固有长宽比，则高度使用(used width)/(intrinsic ratio)
* 如果height是auto，width是指定值，元素有固有高度，则使用该固有高度
* 如果height是auto，width是指定值，但是元素既没有固有高度，也没有固有长宽比，那么高度使用一个特定的长方形的高度，这个长方形是满足长宽比为2:1、高度不能超过150px、宽度不能超过设备宽度这三个条件的最大的长方形。

基本上我想有了上面的依据，inline情况下的展现就能猜测得八九不离十了。

最后附带说一下关于line height的计算方式

规范在inline formatting context中说过，很多inline-level box相连会导致换行，其实每一行里的内容都会包含在一个叫line box的东西里，这个所谓的line box才决定了你看到的一段文字的行间距。关于这个line box的高度，有以下因素要考虑：

1. line box中的每个inline-level box的高度会一一计算一遍。对于inline replaced element, inline-block element和inline-table element，高度就是他们的margin box的高度；而对于inline box（应该是对应inline non-replaced element），高度就是他们的'line-height'。
2. inline-level box在垂直方向上的对齐方式由vertical-align属性决定。
3. 最后line box的高度为一行内最上面的box的顶部到最下面的box的底部。

- References:
  1. [Replaced element](http://www.w3.org/TR/CSS2/conform.html#replaced-element)
  2. [Replaced element](https://developer.mozilla.org/en-US/docs/Web/CSS/Replaced_element)
  3. [CSS2.1 10 Visual formatting model details](http://www.w3.org/TR/CSS21/visudet.html)
  4. [CSS2.1 8 Box model](http://www.w3.org/TR/CSS21/box.html)
  5. [CSS2.1 9.4.2 Inline formatting contexts](http://www.w3.org/TR/CSS21/visuren.html#inline-formatting)
