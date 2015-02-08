title: Block Formatting Context要点
date: 2015-02-08 11:11:28
categories:
- IT
- NOTES
tags:
- css
- block formatting context
- bfc
---
直接看css的w3c规范，发现规范中的内容就如枯燥的法律条文一般，虽然每一条都是非常严谨和有用的，但是文章却没有任何侧重性，读起来相当吃力，犹如啃字典一般。于是对于规范中的block formatting context，我在网上找到一篇讲得很有条理性的文章（引文1），总结一下要点如下。

## 文档里说了什么

### bfc如何flow？

bfc的positioning scheme是**normal flow**，就是说bfc是属于正常文档流的。

### 如何触发bfc?

[css2.1 Section 9.4.1](http://www.w3.org/TR/CSS21/visuren.html#block-formatting)里规定：

- floats
- absolutely positioned elements
- inline-blocks
- table-cells
- table-captions
- elements styled with "overflow"(any value other than "visible")
- elements styled with "display" valued "flex"/"inline-flex"(flex boxes)

<!--more-->

但是[CSS level 3 specification](http://www.w3.org/TR/css3-box/#block-level0)里规定下面的情况也会触发bfc

- The value of " position" is neither "static" nor "relative"

这意味着规范确定了**position:fixed**也会触发bfc。

最后就是fieldset元素会触发bfc。

### bfc有什么用？

bfc能包含浮动内容，阻止collapsing margins，并且不会和浮动内容重叠。

## 实际应用

bfc的行为和block box类似，除了以下情况

### bfc会阻止margin发生塌陷

相邻的block boxes之间的margin会塌陷，但是只限于这两个block boxes都在同一个bfc里的时候。也就是说，如果两个相邻的box属于不同的bfc，那么它们之间margin**不会**发生塌陷。

例子：
<div style="background:skyBlue"><p style="margin:20px"> This is a paragraph inside a DIV with a blue background, styled with <code>margin:20px</code>.</p></div><div style="background:skyBlue"><p style="margin:20px"> This is a paragraph inside a DIV with a blue background, styled with <code>margin:20px</code>.</p></div><div class="gainLayout" style="background:skyBlue;overflow:hidden"><p style="margin:20px"> This is a paragraph inside a DIV with a blue background, it is styled with <code>margin:20px</code>, The parent DIV is styled with <code>overflow:hidden;zoom:1</code>. </p></div>

前面两个box之间的margin发生了塌陷（20px，而非40px），但是对第三个box来说，由于第三个DIV创建了一个bfc，所以第三个box的margin-top没有和第二个的margin-bottom发生塌陷的情况。

### bfc能包含浮动的内容

例子：
<div style="background:skyBlue"><p style="float:left;margin:20px"> This paragraph is a float inside a DIV with a blue background, it is styled with <code>margin:20px</code></p></div><div class="gainLayout" style="background:skyBlue;overflow:hidden;clear:left"><p style="float:left;margin:20px"> This paragraph is a float inside a DIV with a blue background, it is styled with <code>margin:20px</code>. The parent DIV is styled with <code>overflow:hidden;zoom:1</code>. </p></div>

第一个段落由于浮动而脱离了文档流，它的容器发生了塌陷，因此看不到天蓝色背景；第二个段落同样浮动了，但是由于它的容器创造了一个bfc，因此这个容器就能成功“俘获”住这段内容了，因此也就能看到天蓝色背景了。你还会发现，第二个段落也不需要手动清除浮动就能让后面的内容正常显示了，这种现象叫做**self-clearing**。

### bfc不会和浮动内容重叠

根据规范，在同一个bfc里，一个子bfc的border box不会和浮动内容的margin box重叠。这个意思是说，这个子bfc会自动设置隐形的margin来防止和浮动内容重叠，也是因为这个原因，bfc上的负边距不会生效。

例子：
<div style="background:skyBlue;float:left;width:180px;height:100px">aaa</div><div style="background:yellow;float:right;width:180px;height:100px">bbb</div><div class="gainLayout" style="background:pink;overflow:hidden;border:5px solid teal;height:130px">ccc</div>

上图就是实际效果，可以和下面的没有bfc的效果对比

<div style="background:skyBlue;float:left;width:180px;height:100px">aaa</div><div style="background:yellow;float:right;width:180px;height:100px">bbb</div><div class="gainLayout" style="background:pink;border:5px solid teal;height:130px">ccc</div>

## IE下的hasLayout和bfc的对比

你可能会注意到上面的例子里用了**overflow:hidden;zoom:1**，前者是现代浏览器里用来触发bfc的，后者是用来触发IE下（IE 5.5/6/7）的hasLayout。hasLayout的效果和bfc非常类似。（hasLayout就稍微提一下，有兴趣的可以看引文1）

## 总结

看了这篇文章，bfc的概念总算有个稍微具体的认识了。理解bfc应该进阶掌握css过程中至关重要的一环，也只有理解了bfc，才能算css的高级玩家了~

- Reference:
  1. [CSS 101: Block Formatting Contexts](http://yuiblog.com/blog/2010/05/19/css-101-block-formatting-contexts/)
- Related:
  1. [css中的Visual Formatting Model](/blog/2015/01/31/css-visual-formatting-model/)
