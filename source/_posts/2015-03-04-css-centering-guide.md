title: css居中方法汇总
date: 2015-03-04 08:33:23
categories:
- IT
- Notes
tags:
- css
- flexbox
---
css居中很多时候是一件不那么凭**直觉**就能完成的任务，并且方法还千奇百怪，而且适用场景各不相同，所以常常让人头疼。找了一篇汇总的文章，在此总结一下，帮助记忆。

## 水平居中

### inline 或 inline-* 元素（文本和链接）？

{% code lang:css %}
.center-childern {
  text-align: center;
}
{% endcode %}

可以在块级父元素下使内敛元素居中

<!--more-->

### 块级元素？

{% code lang:css %}
.center-me {
  margin: 0 auto;
}
{% endcode %}

把块级元素的margin-left和margin-right设置成auto就能使其居中

### 多个块级元素整体居中成一行？

可以使每个块级元素变成inline-block，然后用text-align:center使其整体居中

{% code lang:css %}
.inline-block-center {
  text-align: center;
}
.inline-block-center div {
  display: inline-block;
  text-align: left;
}
{% endcode %}

或者用flexbox

{% code lang:css %}
.flex-center {
  display: flex;
  justify-content: center;
}
{% endcode %}

## 垂直居中

### inline 或 inline-* 元素（文本和链接）？

* 使上下padding相等，看上去就是垂直居中的，一行或多行的话

* 单对一行来讲，使line-height和height相等也能使其垂直居中

{% code lang:css %}
.center-text-trick {
  height: 100px;
  line-height: 100px;
  white-space: nowrap;
}
{% endcode %}

* 对多行来讲，

一种是用table，当一个容器是table cell（不管是本身就是，还是通过display:table-cell），通过vertical-align就能使内容居中。

{% code lang:css %}
.center-table {
  display: table;
}
.center-table p {
  display: table-cell;
  vertical-align: middle;
}
{% endcode %}

另一种是用flexbox

{% code lang:css %}
.flex-center-vertically {
  display: flex;
  justify-content: center;
  flex-direction: column;
  height: 400px;
}
{% endcode %}

### 块级元素？

* 高度已知？绝对定位，50%，负边距

{% code lang:css %}
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  height: 100px;
  margin-top: -50px; /* account for padding and border if not using box-sizing: border-box; */
}
{% endcode %}

* 高度未知？可以用css3？

{% code lang:css %}
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}
{% endcode %}

* flexbox

{% code lang:css %}
.parent {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
{% endcode %}

## 同时水平和垂直居中

结合上面两类一般就能实现，最后可以归为三类

* 宽高确定

* 宽高不确定

* 用flexbox

{% code lang:css %}
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
{% endcode %}

## 总结

除了上面提到的方法，肯定还有其他比较冷门的方法的，例如引文2中的。

References:
  1. [Centering in CSS: A Complete Guide](https://css-tricks.com/centering-css-complete-guide/)
  2. [Absolute Horizontal And Vertical Centering In CSS](http://www.smashingmagazine.com/2013/08/09/absolute-horizontal-vertical-centering-css/)
Related:
  1. [FlexBox](/blog/2015/01/22/flexbox-concept)
