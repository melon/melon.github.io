title: bash脚本中用$(...)比`...`更好
date: 2014-10-25 20:47:20
categories:
- IT
tags:
- linux
- bash
---
\`COMMANDS\`和$(COMMANDS)两者都是用来在脚本中实现指令替换的，两者都符合POSIX标准，虽然`...`(backticks)这种形式在很多主流脚本中很常见，但是后者是更为推荐的指令替换写法。

举例说明一下两者的不同，以及$(COMMANDS)的优势

- \`COMMANDS\`对指令中的“\”处理很诡异

{% code lang:bash %}
echo "`echo \\a`" "$(echo \\a)"
# a \a

echo "`echo \\\\a`" "$(echo \\\\a)"
# \a \\a

foo=`echo '\\'`; bar=$(echo '\\'); echo "foo is $foo, bar is $bar"
# foo is \, bar is \\

echo "\\"
# \
echo '\\'
# \\
# 如果上面的例子第一个是双引号
foo=`echo "\\"`;
# bash: command substitution: line 1: unexpected EOF while looking for matching `"'
# bash: command substitution: line 2: syntax error: unexpected end of file
{% endcode %}

- $(COMMANDS)在引号嵌套的时候使用更加方便

{% code lang:bash %}
echo "x is $(sed ... <<<"$y")"

echo "x is `sed ... <<<\"$y\"`"
{% endcode %}
第一个例子中的$y两边的引号会被认为是一对的，因为它处于$(...)之中。对于很多熟悉C等语言的程序员来说这或许理所当然，但在bash中不是这样的，正常情况bash的引号匹配可以说是非常落后的。就如第二个例子中体现的，必须用\转义来避免歧义。

- $(COMMANDS)让指令嵌套指令也变得更加方便

{% code lang:bash %}
x=$(grep "$(dirname "$path")" file)

x=`grep "\`dirname \"$path\"\`" file`
{% endcode %}
实际上本质原因还是和上一个例子一样，$(...)创建了一个新的context，使得其中的引号能与外部的引号完全“隔绝”开来。

- $(...)的$符号以及括号在视觉上会比较清晰

- \`符合容易和\'符号混淆

综上，以后写bash脚本的时候还是尽量使用$(...)吧！

- References:
  1. [Obsolete and deprecated syntax](http://wiki.bash-hackers.org/scripting/obsolete)
  2. [Why is $(...) preferred over `...` (backticks)?](http://mywiki.wooledge.org/BashFAQ/082)