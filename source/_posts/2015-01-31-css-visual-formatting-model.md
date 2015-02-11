title: css中的Visual Formatting Model
date: 2015-01-31 15:12:49
categories:
- IT
- Notes
tags:
- css
- visual formatting model
---
visual formatting model的东西大家在平时使用css的时候也许就一直在接触，只是不知道这些所谓的规则还有一个这样学术的名称罢了。这次我阅读和记忆了一下css2中这部分的内容，用来应对面试中可能会遇到的变态问题~

## 9 Visual formatting model

### 介绍

vfm: 关于visual media如何处理document tree的一些规范

vfm模型规定，document tree中的每个元素都会生成零个或多个box（[box model](http://www.w3.org/TR/CSS2/box.html)中定义的box）。这些box的布局由以下因素决定：

- box的类型（type）和尺寸（dimension）
- 定位方式（positioning scheme）（normal flow, float, absolute positioning）
- document tree中元素之间的关系
- 外部因素（例如，viewport的尺寸、图片的固有尺寸等）

containing block：
很多box的位置和尺寸是根据这个长方形的被称为containing block的box算出来的。通常我们说，一个box为它的子元素建立了一个containing block；一个box的containing block指的是容纳这个box的父级box。

每个box会根据它的containing block来定位，但并不一定局限在containing block里，有可能overflow。

<!--more-->

### 控制box的生成

#### block-level elements, block boxes, anonymous block boxes

block-level element: display属性为**block**,**list-item**,**table**时可使元素成为块级元素

block-level box: 块级盒子是指**参与**block formatting context的box。每一个块级元素会生成一个**主块级盒子(principal block-level box)**，这个主块级盒子会包含子级盒子，该主块级盒子就是定位方式所作用的对象。一些块级元素像list-item会生成额外的block-level-box。

block container box: 除了table boxes和replaced elements，一个块级盒子也是一个块级容器盒子。一个块级容器盒子可以只包含块级盒子，也可以通过构建一个inline formattng context从而只包含内联级盒子（inline-level boxes）。不是所有的块级容器盒子是块级盒子：non-replaced inline blocks和non-replaced table cells是块级容器（盒子）但却并不是块级盒子。

block box: 既是块级盒子又是块级容器的叫block box。

{% code lang:html %}
<DIV>
Some text
<P>More text
</DIV>
{% endcode %}

(div和p都默认有display:block)，看似div同时包含了inline content和block content。但是，正如上文所说，我们规定，遇到这种情况，

即**当一个block container box包含了inline content和block-level box, 那么inline content会强制变成anonymous block box。**

当一个inline box包含了一个in-flow的block-level box，这个inline box和它所包含的inline内容将被这个block-level box（或者任何几个连续的或者被collapsible whitespace分割的block elements，或脱离文档流的元素）所破坏，inline内容将分成两个box（既是两边都是空的），这两个都是block-level的box。

{% code %}
<STYLE>
p    { display: inline }
span { display: block }
</STYLE>
<BODY>
<P>
This is anonymous text before the SPAN.
<SPAN>This is the content of SPAN.</SPAN>
This is anonymous text after the SPAN.
</P>
</BODY>
{% endcode %}

p包含了三段，最终body这个box会包含一个anonymous block box，一个block box和另一个anonymous block box。

anonymous box的属性中，能继承的部分是由父级继承而来，剩下的属性都默认是初始值。

如果anonymous block box含有子元素，并且子元素的尺寸涉及到百分比，那么这个百分比的参照并不是anonymous box，而anonymous box的父级元素。

#### inline-level elements, inline boxes, anonymous inline boxes

inline-level element: 这种元素不会产生新的元素块，而是在行内分布。display属性为**inline**,**inline-table**,**inline-block**时可使元素成为内联级元素

inline-level box: inline-level element对应于此

inline box: 既是inine-level的，同时它的内容会参加当前inline容器的inline formatting context。display属性为inline的non-replaced element会创建一个inline box。那些是inline-level box但不是inline box的（像replaced inline-level elements,inline-block elements,inline-table elements）又叫atomic inline-level boxes，因为它们以single opaque box的形式参加它们的inline formatting context。

{% code %}
<p>Some <em>emphasized</em> text</p>
{% endcode %}

(p是block box，em是inline element)，被分割的这两段文本就是anonymous inline boxes。

属性继承同anonymous block boxes。

那些会塌陷的空白符内容将不会产生anonymous inline boxes。

在处理table时，会有更多的anonymous boxes生成。

### positioning schemes

1. Normal flow: 包括block-level boxes的block formatting，inline-level boxes的inline formatting，和block-level boxes以及inline-level boxes的relative positioninig。

2. Floats: 在float模型中，一个box首先会按normal flow的规定排布，然后就脱离文档流，移位到尽可能左或右的位置。

3. Absolute positioning: 在绝对定位中，box完全脱离文档流。

out of flow: 如果一个元素设置成浮动、绝对定位或者是根元素，成为out of flow
in flow: out of flow的反面

#### position属性

relative: 'position:relative'的效果在table-row-group,table-header-group,table-footer-group,table-row,table-column-group,table-column,table-cell,table-caption上未定义

absolute:
though absolutely positioned boxes have margins, they do not **collapse** with other margins

fixed:
同样margin不会collapse；不同设备上的不同表现；

### 正常文档流

正常文档流中的box归属于一个formatting context,是block的或inline的，但只取其一。block-level box参加block formatting context, inline-level box参加inline formatting context。

#### block formatting contexts

下列情况元素会给**它们的内容**创建一个新的bfc
- floats
- absolutely positioned elements
- block containers that are not block boxes(例如inline-blocks,table-cells,table-captions)
- block boxes with 'overflow' other than 'visible'(这个值被冒泡到viewport的情况不算)

在bfc中，boxes在垂直方向上，从containing block的最上边开始，一个一个叠在一起，相邻的boxes之间的间距由margin决定，并且在一个bfc**内部**相邻boxes之间会有**collapse**的情形发生。

在bfc中，每个box的左外边缘（left outer edge）紧贴containing block的左边缘（left edge）（对于rtl，右边缘）。即使是有浮动的情况下也是这样的（虽然一个box的line boxes可能会因为浮动而shrink），除非一个box创建了一个新的bfc（在这种情况下，它因为前一个浮动而不会紧贴左边，而且它的宽度会缩小）。

#### inline formatting contexts

在ifc中，boxes在水平方向上，一个一个紧挨着。水平方向上的margin,border,padding才有效。垂直方向上，boxes可能按bottom,top或者文字的baseline来对齐。那个包含一行的所有box的长方形区域被称为line box。

line box的宽度由containing block和**是否有浮动**决定，高度是由line height的计算机制决定。

line box的高度总是能容纳所有box，而且它的高度可能比它所包含的最高的box还要高一点（例如，当box是按baseline对齐的）。当某个box的高度不足line box的高度时，该box的垂直方向上的位置由vertical-align设定。

通常，line box的左边缘会紧贴它的containing block的左边缘，右边缘也紧贴containing block的右边缘。但是，浮动boxes可能会介于containing block边缘与line box边缘之间，因此有些行的line box的宽度可能会窄于containing block。

如果一个inline box的宽度大于line box，它会分成多行。如果无法分成多行（例如inline box只包含一个字符，或语言特性要求不允许一个单词折行，或inline box受white-space的nowrap或pre影响时），inline box就会overflow。



- References:
	1. [9 Visual formatting model](http://www.w3.org/TR/CSS2/visuren.html)
	2. [MDN Visual formatting model](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Visual_formatting_model)
