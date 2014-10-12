title: "ASCII编码比UTF-8编码好在哪儿？"
date: 2014-09-16 16:26
categories:
- IT
tags:
- performance
---
ASCII编码比UTF-8编码好在哪儿？

看到这句话，我们的第一反应也许是“ASCII？什么老掉牙的玩意儿？它能比UTF-8好？它要是好为啥现在UTF-8这么流行而ASCII却鲜有耳闻？”

诚然，随着互联网的兴起，UTF-8的流行程度也乘风直上，现在的网页，很少不是用UTF-8编码的。但是其实这句话并不是描述这个情景的。

其实我写这篇文章的缘由是这样的。在查看[mongodb配置文件选项](http://docs.mongodb.org/v2.4/reference/configuration-options/)的时候，偶然间看到这句话

<blockquote>
<strong>NOTE:</strong>
<br>
Ensure the configuration file uses ASCII encoding. mongod does not support configuration files with non-ASCII encoding, including UTF-8.
</blockquote>

“不支持非ASCII编码，包括UTF-8”，包括UTF-8！！在这个互联网时代，看到这样的话是很惊讶的。然后去搜索了一些资料，总算明白其中的缘由。

其实“ASCII编码比UTF-8编码好”这句判断的场景通常是在谈论<strong>数据库引擎</strong>等对性能要求比较高的时候。为什么要用ASCII编码而不用UTF-8编码，简单来说，就是在某些场景下使用前者的性能优于使用后者。

那具体来说是为什么呢？

实际上UTF-8是完全兼容ASCII的，就是说一段ASCII编码的字串改用UTF-8编码方式来识别，结果是一样的。两种编码在性能上的差别在于解析UTF-8编码的数据的时候，由于UTF-8编码的某一个字符的字节长度并不像ASCII一样是固定的一个字节，而是不定字节的（按目前的Unicode数量，UTF-8编码的单个字符的字节长度范围是1-4个字节），因此在读取完第一个字节之后，有时候并不能立即确定第一个字符到底是哪个字符，很有可能必须结合后几个字节的数据才能最终判断出那个字符。由于多了这些判断，性能上UTF-8肯定比不上ASCII。鉴于配置文件完全可以规定只用ASCII字符来写，为啥不用更加简单明了的ASCII编码呢？

<pre>
e5 ae 9d e9 a9 ac 58 35
</pre>

当给出上述的十六进制数据的时候，如果已知是UTF-8编码的数据，那么从一段看不懂的数据到可以被人识别的文本时，到底经历了什么样的过程？首先，我们会读取第一个字节e5，由于前4个bit组成了十六进制的e，根据这个，就可以得知第一个字符是占有三个字节的（具体为什么是这样，可以参阅UTF-8编码的内容），那么后两个字节都读取之后，我们就能确定第一个字符为简体中文“宝”；然后类似的，第四个字节的前4bit是e，这个字节和后俩字节一起表示一个字符，实际上是简体中文“马”字。然后是第七个字节58，对ASCII码熟悉的童鞋肯定知道，58就是代表大写字母"X"，同样的，35表示数字"5"。

我们可以设想一下，如果仅有后俩字符，并且知道是ASCII编码的，那么每个字符就仅能用一个字符表示，也就不存在上述多个字符的情况需要考虑了，性能上肯定是更优了的。


- References:
    1. [What is the advantage of choosing ASCII encoding over UTF-8?](http://programmers.stackexchange.com/questions/97247/what-is-the-advantage-of-choosing-ascii-encoding-over-utf-8)