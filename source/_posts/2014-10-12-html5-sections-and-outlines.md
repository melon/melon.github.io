title: html5中的section和outline概念
date: 2014-10-12 12:40:40
categories:
- IT
tags:
- frontend
- html
---
一些概念：
<pre>
section: 区域、区块
outline: 大纲

sectioning root:
blockquote,body,details,dialog,fieldset,figure,td

sectioning content, sectioning elements（分区元素）:
article,aside,nav,section

heading content, heading elements:
h1,h2,h3,h4,h5,h6,hgroup
</pre>

注意：以下概念目前应该还只是个规范，在浏览器似乎尚未有应有的范例。

## HTML5的优势

文档的语义结构是由section和sub-section来描述的，文档被划分成一块块区域之后，就有了一个outline（大纲）。在HTML4时代，并不存在显性的section标签，文档中section的划分是由heading elements（h1，h2，h3，h4，h5，h6，hgroup）来隐性地(implicitly)决定的，HMML4文档完全靠这几个元素来确定文档的结构和它的outline。

但是，HMTL4对于文档结构的定义以及outlining(获取大纲的)算法过于粗略，引发了很多问题：

1.HTML4对于什么是一个区块（section），以及它的作用域（scope）是如何定义的问题没有明确的说明，这导致由文档自动化生成文档大纲变得几乎不可能。而文档大纲自动生成有时候是个比较重要的东西，特别是针对残疾人的一些辅助技术会依赖于这个。所以HTML5移除了div元素对文档结构的产生的影响，并且引入了section元素。

2.HMTL4在合并不同文档时会比较困难。将一个文档包含到另一个文档中的时候，很可能意味着这个子文档的结构需要改变（heading元素的级别可能需要做相应的降低才能在合并时保持住原有结构）。因此在HTML5中，引入了四个分区元素（article，section，nav，aside），这四个元素会对文档结构造成影响(注意：header和footer并不会对文档结构产生影响)。

3.在HTML4中，任何一个区块都是文档大纲的一部分。但是很多时候，在文档中我们会包含一些不应该是文档的一部分、但却又和文档有些联系的内容，比如广告区块、解释性的区块。为此，HTML5引入了aside元素，用来描述这样的并不应该包含在主文档大纲的内容。

4.同样是因为在HTML4中任何一个区块都是文档大纲的一部分，那些应该和整个网站相关的内容也被包含到文档大纲里了，比如logo、菜单、目录、版权信息、用户协议等内容。所以，HMTL5引入了一些元素，比如nav可以用来包含一个链接组或链接式的目录，header和footer里可以放一些和网站相关的信息。

总的来说，就是HMTL5明确了分区的一些概念，使得文档大纲能被浏览器分析出来，从而能被利用来提升用户体验。


## 显性分区（explicit sectioning）

前面提到，只有这四个元素article，section，nav，aside是具有显性分区作用的，另外h1-h6，hgroup这几个元素有隐形分区的效果，文档中的这11个元素决定了大纲的结构。首先看个例子

{% code lang:html %}
<body>
  <h1>Apples</h1>
  <p>Apples are fruit.</p>
  <section>
    <h1>Taste</h1>
    <p>They taste lovely.</p>
    <section>
      <h1>Sweet</h1>
      <p>Red apples are sweeter than green ones.</p>
    </section>
  </section>
  <section>
    <h1>Colour</h1>
    <p>Apples come in various colours.</p>
  </section>
</body>
{% endcode %}

上面这个例子完全显性地定义了文档的大纲结构。可以发现，我们用了多个相同的h1标签，但是这些h1标签能来描述不同的层级，这在HMTL4里是做不到的。上面文档形成的大纲结构如下

{% code lang:html %}
1. Apples
   1.1 Taste
       1.1.1 Sweet
   1.2 Colour
{% endcode %}

采用相同的h1标签的一个好处就是在维护的时候方便了，例如在编辑的时候如果想提升或降低层级，无需再去繁琐地修改标签名了。当然也有人更倾向于明确地指出层级，直接从代码就能一目了然地看出文档结构，并且不同的heading标签在样式上处理也更为方便，如下

{% code lang:html %}
<body>
  <h1>Apples</h1>
  <p>Apples are fruit.</p>
  <section>
    <h2>Taste</h2>
    <p>They taste lovely.</p>
    <section>
      <h3>Sweet</h3>
      <p>Red apples are sweeter than green ones.</p>
    </section>
  </section>
  <section>
    <h2>Colour</h2>
    <p>Apples come in various colours.</p>
  </section>
</body>
{% endcode %}

## 隐性分区（implicit sectioning）

由于HTML5中的显性分区元素（article，section，nav，aside）并不要求强制使用，同时又考虑到需要保持对HTML4文档的兼容性，这个时候隐性分区显得就有必要了。

上面的例子如果希望用隐性分区方式得到相同的大纲结构，会怎么写呢？

{% code lang:html %}
<body>
  <h1>Apples</h1>
  <p>Apples are fruit.</p>
  <h2>Taste</h2>
  <p>They taste lovely.</p>
  <h3>Sweet</h3>
  <p>Red apples are sweeter than green ones.</p>
  <h2>Colour</h2>
  <p>Apples come in various colours.</p>
</body>
{% endcode %}

上面的代码很显而易见，如果稍微改动一下

{% code lang:html %}
<body>
  <h1>Apples</h1>
  <p>Apples are fruit.</p>
  <h2>Taste</h2>
  <p>They taste lovely.</p>
  <h3>Sweet</h3>
  <h3>Hot</h3>
  <h4>Very Hot</h4>
  <h3>Bitter</h3>
  <h2>Colour</h2>
  <p>Apples come in various colours.</p>
</body>
{% endcode %}

得到的结构就是

{% code lang:html %}
1. Apples
   1.1 Taste
       1.1.1 Sweet
       1.1.2 Hot
             1.1.2.1 Very Hot
       1.1.3 Bitter
   1.2 Colour
{% endcode %}

看着这种转化过程是不是看着有种“堆栈”的感觉？其实生成大纲的算法里确实用到了stack。

说到这，向大家推荐一个由文档生成大纲的在线生成工具，由一位程序员所写，[HTML5 Outliner](http://hoyois.github.io/html5outliner/)

## 分区根元素（sectioning roots）

所谓分区根元素，就是指这个元素自己的子元素能形成一个大纲，但是这个大纲并不会包含在这个元素的祖先节点（ancestor）的大纲之中，含有这种特点的元素有body，blockquote，details，dialog，fieldset，figure，td。

{% code lang:html %}
<section>
  <h1>Forest elephants</h1>
  <section>
    <h2>Introduction</h2>
    <p>In this section, we discuss the lesser known forest elephants</p>
  </section>
  <section>
    <h2>Habitat</h2>
    <p>Forest elephants do not live in trees but among them. Let's
       look what scientists are saying in "<cite>The Forest Elephant in Borneo</cite>":</p>
    <blockquote>
       <h1>Borneo</h1>
       <p>The forest element lives in Borneo...</p>
    </blockquote>
  </section>
</section>
{% endcode %}

这个例子的大纲是

{% code lang:html %}
1. Forest elephants
   1.1 Introduction
   1.2 Habitat
{% endcode %}

可以看到blockquote里的大纲并未包含在主大纲（main outline）中。

## 大纲生成算法（Outline Algorithm）

具体算法过程可以参阅 [大纲生成算法](https://html.spec.whatwg.org/multipage/semantics.html#outlines)

## 一些例子

### 1.

{% code lang:html %}
<section>
  <h1>Apples</h1>
  <p>Pomaceous.</p>
  <h1>Bananas</h1>
  <p>Edible.</p>
  <h1>Carambola</h1>
  <p>Star.</p>
</section>
{% endcode %}

上面这个例子很有迷惑性，其实它的大纲是

{% code lang:html %}
1. Apples
2. Bananas
3. Carambola
{% endcode %}

而不是

<del>1. Apples</del>
   <del style="margin-left:1em;">1.1 Bananas</del>
   <del style="margin-left:1em;">1.2 Carambola</del>

这是因为当存在同级头元素时，后一个头元素会终结前一个同级元素的区块，然后自己新建一个区块

### 2.

{% code lang:html %}
<section>
  <section>
    <h1>A plea from our caretakers</h1>
    <p>Please, we beg of you, send help! We're stuck in the server room!</p>
  </section>
  <h1>Feathers</h1>
  <p>Epidermal growths.</p>
</section>
{% endcode %}

大纲是

{% code lang:html %}
1. (untitled section)
  1.1 A plea from our caretakers
2. Feathers
{% endcode %}

“Feathers”这个标题不会成为第一个section的标题，如果section下的第一个头元素之前没有子区块，那么这个头元素就是父区块的标题，否则它就会自动新形成一个区块。

### 3.

所以，在一个article元素中，如果第一个头元素h1不在第一个子区块之前的话，那么这个h1标题从大纲的角度来说就不是这个article元素的标题了。

{% code lang:html %}
<!DOCTYPE HTML>
<title>We're adopting a child! — Ray's blog</title>
<h1>Ray's blog</h1>
<article>
  <header>
    <nav>
      <a href="?t=-1d">Yesterday</a>;
      <a href="?t=-7d">Last week</a>;
      <a href="?t=-1m">Last month</a>
    </nav>
    <h1>We're adopting a child!</h1>
  </header>
  <main>
    <p>As of today, Janine and I have signed the papers to become
    the proud parents of baby Diane! We've been looking forward to
    this day for weeks.</p>
  </main>
</article>
{% endcode %}

它的大纲是

{% code lang:html %}
1. Ray's blog
   1.1 Untitled article
       1.1.1 Untitled navigation section
   1.2 We're adopting a child!
{% endcode %}

## 总结

section和outline应该算HTML语义化里比较重要的一个东西，之前一直觉得语义化是个很虚很没意思的东西，这次研究了一下发现还蛮有说法的。

- References:
  1. [Sections and Outlines of an HTML5 Document](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Sections_and_Outlines_of_an_HTML5_document)
  2. [4.3 Sections](https://html.spec.whatwg.org/multipage/semantics.html#sections)