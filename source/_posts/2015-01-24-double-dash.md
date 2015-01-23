title: bash脚本中的--(double dash)
date: 2015-01-24 00:41:00
categories:
- IT
tags:
- bash
- git
---

git checkout的manual里有这样一句

{% code lang:bash %}
git checkout [-p|--patch] [<tree-ish>] [--] [<paths>...]
{% endcode %}

<!--more-->

我们可能会这么用

{% code lang:bash %}
git checkout -- melon.github.io/blog/post/double-dash.md
{% endcode %}

这句话的意思是根据index状态来恢复melon.github.io/blog/post/double-dash.md这个文件，通常情况下就是撤销改动。但--这个符号是什么意思？

<blockquote style="text-align:left;">
More precisely, a double dash (--) is used in bash built-in commands and many other commands to signify the end of command options, after which only positional parameters are accepted.
</blockquote>

就是说--是用来消除歧义的，--之后的内容将视为位置性参数（positional parameters）了。

有了这个，下面这种歧义就不会发生了

{% code lang:bash %}
git checkout -- master
{% endcode %}

假如刚好当前文件夹下有个文件叫master，如果你不加--，git会认为是你想checkout到master分支。

类似的，其他bash指令也会用到--

{% code lang:bash %}
echo 'sss -v rrrr-veeee' | grep -- -v
{% endcode %}


-- References:
  1. [What does “--” (double-dash) mean? (also known as “bare double dash”)](http://unix.stackexchange.com/questions/11376/what-does-double-dash-mean-also-known-as-bare-double-dash)
