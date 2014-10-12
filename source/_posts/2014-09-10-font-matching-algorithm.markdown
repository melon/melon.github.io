title: "字体匹配算法"
date: 2014-09-10 17:14
categories:
- IT
tags:
- frontend
---
最近偶然间在Chrome的开发者工具栏下看到这么一小模块

![rendered fonts](/images/custom/2014-09-10-font-matching-algorithm-1.png)

很显然它的意思是，当前选中的元素下的字符用到了12个TakaoPGothic字体的字形(glyph)和5个WenQuanYi Micro Hei字体的字形。那浏览器到底是怎么决定最后显示我们所看到的字呢？

为此我特意去研究了一下w3c关于字体方面的规范。我所研究的是CSS3的字体部分的规范，与目前最通用的CSS2.1的规范虽然有些许出入，但是字体匹配这一块基本上是没有比较大的不同的。

我在下面将大致复述一下规范中关于字体匹配算法的部分，如果想更好的理解字体渲染，建议把整篇CSS3字体的规范研读一遍。

<pre style="word-wrap:break-word;">
浏览器在决定某个字符到底该用哪种字体的哪个字形来展示的时候，经过了以下的步骤：

1.对于某个元素下的文本节点里的字符，首先浏览器会计算出这些字符的最终的font-family属性，然后挑选该属性中的第一个指定的字体进行下一步。

2.如果第一个字体的关键字是一个通用字体(generic family)，比如serif/sans-serif/monospace，浏览器就会在通用类型中挑一个合适的字体来使用。关于挑选的标准，浏览器可能会以字
符的语言类型或者字符所处的Unicode范围为依据。

3.对于其他的非通用的字体，浏览器首先会优先去查看开发者是否用@font-face规则自定义了相应的字体，然后才会去查看可用的系统字体中是否有这个字体。如果@font-face中有定义当前字体，但是却不可用或者没有相应的数据，则视为该字体并不存在，对于这种情况，浏览器不会再去查找系统中是否还含有该同名的字体。

4.如果匹配到了某字体，浏览器就会准备好该字体的字体集(the set of font faces)，然后根据下面指定的其他字体属性的数据来逐渐缩小该字体集的范围，直到最终匹配到某个特定的字形。在这一步当中，对于用@font-face定义的有着相同的字体名称，但是有不同的unicode-range的规则组，统一视为一个复合字体(composite face)。具体的其他字体属性的说明如下：

    a.首先是font-strech，这是CSS3新引入的字体属性，因为不常用，详情就不在这赘述了。反正这个属性处理过后，符合的字体留下，不符合的从备选的字体集中剔除，字体集进一步缩小。

    b.font-style。简单来说，如果font-style是italic，浏览器首先会查看是否有italic类型的字体，没有的话再查看是否有oblique类型的字体，再没有的话查看是否有normal类型的字体，一旦确定有某种类型的字体之后，其他类型的字体将被剔除出字体集。类似的如果font-style是oblique，就按oblique->italic->normal的顺序查看；如果font-style是normal，就按normal->oblique->italic的顺序。其他该条属性的说明请自行查阅。

    c.font-weight。这一步会将字体集缩小到一个字形。可能字体中100-900每级字体并不都有，规范中规定实际中会选取weight值相近的字体。具体的选取规则可以参阅font-weight的详细说明。

    d.font-size。根据字体大小形成最终的字形。

5.如果匹配到的字体是由@font-face规则定义的，那必须用以下的方式来选择出一个字形：

a.如果字体资源尚未被加载，并且指定的字符范围包含当前的目标字符，那么就下载字体资源

b.下载之后，如果最终的有效字符映射(effective character map)支持这个目标字符，那么就选择这个目标字符对应的字形

如果是上面提到的复合字体，那么就要按照@font-face定义复合字体时相反的顺序来一个一个应用上面的两条规则。

6.如果找不到当前的font-family，或者有这个font-family但是其中并不包含我们想要的目标字符的字形，那么就会轮到font-family中的下一个字体，让这个新的字体重复上述3个步骤。

7.如果遍历完font-family还是没有找到，那么浏览器就会启用系统字体回退(system font fallback)过程，来找到尽可能合适的字体。不同浏览器可能在这一步会有不同的结果。

8.如果最终还是没有办法找到某个字符相应的字形，浏览器就应该采用某些方式来告诉用户这些未能成功展示的字符，要么用某个字符特定的丢失字形
<div style="border: 2px solid blue;float:left;color:blue;line-height:80%;">&nbsp;0&nbsp;0&nbsp;<br>
&nbsp;2&nbsp;0&nbsp;</div><br>
要么就直接展示一个丢失字形的符号
<div style="border: 2px solid blue;float:left;color:blue;line-height:80%;">&nbsp;&nbsp;<br>
&nbsp;&nbsp;</div>

</pre>

字体匹配大体的过程就是如上所述，但其中的一些细节以及一些特殊处理的情况，这里并没有详细复述，如果想了解得更透彻一点，还是有必要读读原文规范的。

- References:
    1. [CSS Fonts Module Level 3](http://www.w3.org/TR/css3-fonts/#font-matching-algorithm)