title: FlexBox
date: 2015-01-22 19:13:14
categories:
- IT
- Notes
tags:
- css
- css3
- flexbox
---
flexbox是一个整体的概念，包含了很多新增的css属性。这些属于一部分是作用在所谓的flex容器（flex container）上，另一部分作用在容器中的条目（flex items）上。

flexbox创造了新方向概念：main axis方向和cross axis方向

![](http://cdn.css-tricks.com/wp-content/uploads/2011/08/flexbox.png)

注意main axis的方向不一定是水平的，但main axis和cross axis保持垂直。

<!--more-->

## container上的属性

### display

{% code lang:css %}
.container{
    display: flex | inline-flex;
}
{% endcode %}

### flex-direction

![](http://cdn.css-tricks.com/wp-content/uploads/2014/05/flex-direction1.svg)

{% code lang:css %}
.container {
    flex-direction: row | row-reverse | column | column-reverse;
}
{% endcode %}

### flex-wrap

![](http://cdn.css-tricks.com/wp-content/uploads/2014/05/flex-wrap.svg)

默认flexbox会设法使所有item都放在一行/一列上，但是可以通过flex-wrap来折行/折列。

{% code lang:css %}
.container{
    flex-wrap: nowrap | wrap | wrap-reverse;
}
{% endcode %}

### flex-flow

{% code lang:css %}
.container{
    flex-flow: <‘flex-direction’> || <‘flex-wrap’>
}
{% endcode %}

### justify-content

![](http://cdn.css-tricks.com/wp-content/uploads/2013/04/justify-content.svg)

决定main axis方向上item的排列方式

{% code lang:css %}
.container {
    justify-content: flex-start | flex-end | center | space-between | space-around;
}
{% endcode %}

### align-items

![](http://cdn.css-tricks.com/wp-content/uploads/2014/05/align-items.svg)

决定cross axis方向上item的排列对齐方式

{% code lang:css %}
.container {
    align-items: flex-start | flex-end | center | baseline | stretch;
}
{% endcode %}

### align-content

![](http://cdn.css-tricks.com/wp-content/uploads/2013/04/align-content.svg)

当cross axis方向还有空间时，这个属性就生效了。

注意：当items只有一行时，这个属性不起作用。

{% code lang:css %}
.container {
    align-items: flex-start | flex-end | center | baseline | stretch;
}
{% endcode %}

## item上的属性

### order

![](http://cdn.css-tricks.com/wp-content/uploads/2013/04/order-2.svg)

{% code lang:css %}
.item {
    order: <integer>;
}
{% endcode %}

### flex-grow

![](http://cdn.css-tricks.com/wp-content/uploads/2014/05/flex-grow.svg)

元素膨胀，如果所有元素的flex-grow都设置成1，然后某个元素设置成了2，那么这个元素的大小会是别的元素的两倍

{% code lang:css %}
.item {
    flex-grow: <number>; /* default 0 */
}
{% endcode %}

### flex-shrink

元素收缩

{% code lang:css %}
.item {
  flex-shrink: <number>; /* default 1 */
}
{% endcode %}

### flex-basis

{% code lang:css %}
.item {
    flex-basis: main-size | <‘width’>
}
{% endcode %}

main-size即width或height的值，根据main axis的方向决定。这里如果flex-basis的值取为默认值main-size。这个概念有些复杂，详见w3c文档。

### flex

{% code lang:css %}
.item {
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ] /* default: 0 1 auto */
}
{% endcode %}

### align-self

![](http://cdn.css-tricks.com/wp-content/uploads/2014/05/align-self.svg)

用来覆盖默认的对齐方式或由container的属性align-items所设定的对齐方式。

{% code lang:css %}
.item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
{% endcode %}

注意，**float**,**clear**和**vertical-align**不会对一个flexbox元素产生效果

## 附

flexbox除了在IE10上支持有些恶心之外（不支持IE10以下的版本），在其他版本浏览器，无论是pc或是手机上，勉强都能够支持了。尤其在手机上，除了一些老版本的支持度有限，基本上可以开始大范围用flexbox了。我觉得是时候可以开始尝试使用了。

附上flexbox的hack

https://github.com/melon/flexbox-mixin


- References:
    1. [A Complete Guide to Flexbox](http://css-tricks.com/snippets/css/a-guide-to-flexbox/)
    2. [Using Flexbox: Mixing Old and New for the Best Browser Support](http://css-tricks.com/using-flexbox/)
    3. [Flexible Box Layout Module](http://caniuse.com/#feat=flexbox)
    4. [CSS Flexible Box Layout Module Level 1](http://www.w3.org/TR/css-flexbox-1/)