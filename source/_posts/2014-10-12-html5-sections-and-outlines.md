title: html5中的section和outline概念
date: 2014-10-12 12:40:40
categories:
- IT
tags:
- frontend
---
一些概念：
<pre>
section: 区域、区块
outline: 大纲

sectioning root:
blockquote,body,details,dialog,fieldset,figure,td

sectioning content, sectioning elements:
article,aside,nav,section

heading content, heading elements:
h1,h2,h3,h4,h5,h6,hgroup
</pre>

文档的语义结构是由section和sub-section来描述的，文档被划分成一块块区域之后，就有了一个outline（大纲）。在HTML4时代，并不存在显性的section标签，文档中section的划分是由div元素和heading elements（h1，h2，h3，h4，h5，h6，hgroup）来隐性地(implicitly)决定的，HMML4文档完全考这几个元素来确定文档的结构和它的outline。

但是，HMTL4对于文档结构的定义以及outlining(获取大纲的)算法过于粗略，引发了很多问题：

1.HTML4对于什么是一个区块（section），以及它的作用域（scope）是如何定义的问题没有明确的说明，这导致由文档自动化生成文档大纲变得几乎不可能。而文档大纲自动生成有时候是个比较重要的东西，特别是针对残疾人的一些辅助技术会依赖于这个。所以HTML5移除了div元素对文档结构的产生的影响，并且引入了section元素。

2.HMTL4在合并不同文档时会比较困难。将一个文档包含到另一个文档中的时候，很可能意味着这个子文档的结构需要改变（heading元素的级别可能需要做相应的降低才能在合并时保持住原有结构）。因此在HTML5中，引入了四个分区元素（article，section，nav，aside），这四个元素会对文档结构造成影响(注意：header和footer并不会对文档结构产生影响)。

3.在HTML4中，任何一个区块都是文档大纲的一部分。但是很多时候，在文档中我们会包含一些不应该是文档的一部分、但却又和文档有些联系的内容，比如广告区块、解释性的区块。为此，HTML5引入了aside元素，用来描述这样的并不应该包含在主文档大纲的内容。

4.同样是因为在HTML4中任何一个区块都是文档大纲的一部分，那些应该和整个网站相关的内容也被包含到文档大纲里了，比如logo、菜单、目录、版权信息、用户协议等内容。所以，HMTL5引入了一些元素，比如nav可以用来包含一个链接组或链接式的目录，header和footer里可以放一些和网站相关的信息。

总的来说，就是HMTL5明确了分区的一些概念，使得文档大纲能被浏览器分析出来，从而能被利用来提升用户体验。

- References:
    1. [Sections and Outlines of an HTML5 Document](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Sections_and_Outlines_of_an_HTML5_document)
    2. [4.3 Sections](https://html.spec.whatwg.org/multipage/semantics.html#sections)