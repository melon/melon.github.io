title: "bash语法中重定向的一些奇特用法"
date: 2014-09-28 12:22
categories:
- IT
tags:
- linux
- security
- bash
---
今天看了[耗子叔](http://t.sina.com.cn/haoel)新发的关于最近那个沸沸扬扬的bash漏洞的一篇博客[bash代码注入的安全漏洞](http://coolshell.cn/articles/11973.html)，文章浅显易懂，基本上比较好理解。唯一让我疑惑了一会儿的地方是这个指令

{% code lang:bash %}
>echo date
{% endcode %}

这个“>”，之前没有见过这种将“>”前置的用法，耗子叔也没作太多解释，一言带过，说它其实是个重定向，和下面的指令有相同的效果

{% code lang:bash %}
date > echo
{% endcode %}

在终端里尝试执行了这两条指令，果然效果相同。这着实很让人惊讶，所以我觉得有必要加深一下对bash中的重定向(Redirection)的了解，遂有下文。

bash中的重定向能让我们有机会去控制终端中的输出应该输出到哪儿、输入应该从哪儿输入。配合管道(pipe)的使用，能让bash脚本变得十分强大。

正常情况下，有3个文件是和你的终端相连的，分别可以用文件描述附0,1,2来引用

<pre>
stdin   0   standard input stream (e.g. keyboard)
stdout  1   standard output stream (e.g. monitor)
stderr  2   standard error output stream (usually also on monitor)
</pre>

下面有一些用法

{% code lang:bash %}
# 将 cat text.txt 命令的标准输出stdout 重定向到 bit bucket(/dev/null)
cat text.txt 1>/dev/null
{% endcode %}

{% code lang:bash %}
# echo "There was an error" 这段命令的标准输出stdout 重定向到它的stderr
echo "There was an error" 1>&2
{% endcode %}
这里的2前面之所以有个“&”，是为了防止混淆，是为了让程序清楚到底是stderr还是一个名叫“2”的文件

{% code lang:bash %}
# 让程序静默...
COMMAND ARG >/dev/null 2>&1
{% endcode %}


<h3>有效的SOURCE和TARGET</h3>
在匹配重定向的时候，SOURCE或TARGET必须要是以下几种时才能形成有效的重定向
<pre style="word-wrap:break-word">
<span style="color:red">FILENAME</span>            references a normal, ordinary filename from the filesystem (which can of course be a FIFO, too. Simply everything you can reference in the filesystem)
<span style="color:red">&N</span>                  references the current target/source of the filedescriptor N ("duplicates" the filedescriptor)
<span style="color:red">&-</span>                  closes the redirected filedescriptor, useful instead of > /dev/null constructs (> &-)
<span style="color:red">/dev/fd/N</span>           duplicates the filedescriptor N, if N is a valid integer
<span style="color:red">/dev/stdin</span>          duplicates filedescriptor 0 (stdin)
<span style="color:red">/dev/stdout</span>         duplicates filedescriptor 1 (stdout)
<span style="color:red">/dev/stderr</span>         duplicates filedescriptor 2 (stderr)
<span style="color:red">/dev/tcp/HOST/PORT</span>  assuming HOST is a valid hostname or IP address, and PORT is a valid port number or service name: redirect from/to the corresponding TCP socket
<span style="color:red">/dev/udp/HOST/PORT</span>  assuming HOST is a valid hostname or IP address, and PORT is a valid port number or service name: redirect from/to the corresponding UDP socket
</pre>

<h3>重定向stdout（覆盖）</h3>
{% code lang:bash %}
N> TARGET
{% endcode %}
如果N未指定，则默认为1，即stdout。
具体用法
{% code lang:bash %}
1> output.txt echo 'this is input'
{% endcode %}
1> output.txt 后面的指令原本会输出内容至stdout，但因为这个重定向，会将stdout的内容覆盖到output.txt中
{% code lang:bash %}
> output.txt
{% endcode %}
快速清空文件
{% code lang:bash %}
cat foo.txt bar.txt >new.txt
cat >new.txt foo.txt bar.txt
>new.txt cat foo.txt bar.txt
{% endcode %}
上面三条指令有相同的效果

<h3>重定向stdout（附加）</h3>
{% code lang:bash %}
N>> TARGET
{% endcode %}
跟覆盖相比，唯一区别就是写入前不清除文件

<h3>重定向stdout和stderr（覆盖）</h3>
{% code lang:bash %}
&> TARGET
{% endcode %}
{% code lang:bash %}
>& TARGET
{% endcode %}
以上两种写法效果都和下面的写法等效，下面的写法其实有种嵌套的感觉，重定向中嵌套重定向
{% code lang:bash %}
> TARGET 2>&1
{% endcode %}

<h3>重定向stdout和stderr（附加）</h3>
{% code lang:bash %}
&>> FILE
{% endcode %}
{% code lang:bash %}
>> FILE 2>&1
{% endcode %}

<h3>传输stdout和stderr到管道</h3>
{% code lang:bash %}
COMMAND1 2>&1 | COMMAND2
{% endcode %}
{% code lang:bash %}
COMMAND1 |& COMMAND2
{% endcode %}
利用管道使输出在不同命令间传递

个人认为目前对我来说比较实用的就这些重定向指令，想了解更多可以看引文1或者查阅其他资料。

- References:
    1. [Redirection](http://wiki.bash-hackers.org/syntax/redirection)
    2. [In the shell, what is “ 2>&1 ”?](http://stackoverflow.com/questions/818255/in-the-shell-what-is-21)