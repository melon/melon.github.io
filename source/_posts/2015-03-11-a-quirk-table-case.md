title: 一种奇怪的table展现问题
date: 2015-03-11 08:57:46
categories:
- IT
tags:
- css
- table
- w3c
---
最近v2ex上有位v友问了个css的[奇怪的展现问题](https://v2ex.com/t/175855)，且不论html代码是如何的“不常规”，我的兴趣点在于代码那么写为什么会出现这种“非直觉”的效果？

翻了css2.1规范，总算找到些蛛丝马迹。

该v友的jsfiddle上的demo我fork了一份 http://jsfiddle.net/XXXLtender/5jvcoj98/

从demo中可以看出，由于右下角的那块的ul里面没有元素li的内容，导致了相邻的两个inline-table垂直方向上没有对齐。

你或许会很奇怪，inline-table内部元素的一些不同竟会导致整个inline-table在垂直方向上发生一些变化，这太匪夷所思了。。。

于是我去翻阅了一下规范，找到相关的部分，果然找出些蛛丝马迹。

首先我想到既然是inline-table这样的inline-level的元素，它们在水平方向上的展现形式是排成一行，它们的box会被包含在line box里。那么排成一行时它们垂直方向上的对齐方式总有些讲究吧，没指定vertical-align，默认的应该是baseline。

所谓按baseline排列，规范里说了

**Align the baseline of the box with the baseline of the parent box.**

即将inline元素的baseline和这行的line box的baseline对齐。

但是，既然两个inline-table都是按baseline排列，那么为什么会有不同效果？

那只能说明这两个inline-table自身的baseline位置其实是不同的。

baseline的位置难道和inline-table内部的内容也有关系？

然后在规范里，我果然找到了这句

**The baseline of an 'inline-table' is the baseline of the first row of the table.**

一个inline-table元素的baseline是由table中第一行的baseline决定的。。

那么第一行的baseline又是怎么决定的？

**The maximum distance between the top of the cell box and the baseline over all cells that have 'vertical-align: baseline' is used to set the baseline of the row. **

就是说在一行中所有对齐方式为'vertical-align: baseline'的table-cell之间，挑table-cell的顶端和它的baseline之间距离最大的那个，最为整行的baseline的依据。如下图示例

![](http://www.w3.org/TR/2011/REC-CSS2-20110607/images/cell-align.png)

五个box中，只有1和2是按baseline对齐的，这两个中，2又是顶端和baseline之间距离最大的那个，所以它就是这整行baseline的依据。

另外还有依据很关键的话

**If a row has no cell box aligned to its baseline, the baseline of that row is the bottom content edge of the lowest cell in the row.**

是说假如一行里的所有table-cell都不是按baseline进行对齐的，那么这一行的baseline就是位置最低的那个table-cell的bottom content edge。

这样一来，demo中的所有现象都解释得通了。

最后，告诫一句，能避免用table的地方就不要用table了。。

- References:
  1. [CSS2.1 10 Visual formatting model details](http://www.w3.org/TR/CSS21/visudet.html)
  2. [CSS2.1 17 Tables](http://www.w3.org/TR/2011/REC-CSS2-20110607/tables.html)
