title: bc命令中的ibase和obase
date: 2015-01-08 15:27:39
categories:
- IT
tags:
- bash
- linux
- bc
---
今天在看“贵系”牛人的一篇[博客](http://ppwwyyxx.com/2014/Student-Festival-Puzzle-2014/)的时候，看到了他在“解密”的时候用了bc指令。bc指令使得在脚本中做数学计算变得特别方便，因此脚本中一旦涉及稍微复杂一点的数学计算，bc都是必备良方。该牛人在进制转换的时候用到了bc，他是利用了bc指令中的两个特殊的变量**ibase**和**obase**，从字面上很容易猜到一个是描述输入时数据的进制，另一个是输出时的数据进制。

但是如果你是对bc不大熟悉，看到这样一句指令

{% code lang:bash %}
echo "ibase=2;obase=10000;$(strings poster.jpg  | tail -n1)" | bc
{% endcode %}

你肯定会纳闷：“这里为什么是10000？”为什么输出时的进制要设为10000？难道有什么特殊的含义么？为什么不使用16进制或8进制作为输出的进制，而使用10000进制？？难道是这个活动历来的约定俗成的一个东西？

然而，稍微去深入了解一下ibase和obase之后，你就明白你想太多了。。。

事实是这样的：

当你将ibase设置为2之后，后面的所有数字就默认会被当成2进制来处理了，**包括设定obase时指定的那个数**。

所以，10000。。。其实是个2进制，那么，默算一下，就是16。。。。

附：如果你觉得这样很难接受，你可以把obase的设置提到前面来，效果是一样的。

{% code lang:bash %}
echo "obase=16;ibase=2;$(strings poster.jpg  | tail -n1)" | bc
{% endcode %}
