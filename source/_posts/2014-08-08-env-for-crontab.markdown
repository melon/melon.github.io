title: "关于crontab脚本执行时的环境变量"
date: 2014-08-08 11:09
categories:
- IT
tags:
- linux
---
对于crontab脚本执行时的环境变量感兴趣的原因其实与我之前的一篇文章[Node v0.10.30下的setTimeout问题](/blog/2014/08/05/node-settimeout-bug/)有点关系，之前我不是因为node v0.10.30的一个bug而把node版本降到了v0.10.29了嘛，本以为一切ok了，但是第二天却还是发现服务器所有核心都被占满，几乎卡死，最后只得重启。

我明明记得降版本之后测试执行了node程序，明显已经没问题了，但是为什么最后程序还是卡死了呢？看了一下crontab的脚本之后，立马就明白了可能是crontab执行时的环境变量和目前命令行会话下的环境变量不同的问题。之所以从这个角度考虑问题是，我之前用[n](https://github.com/visionmedia/n)装了不同版本的node，但这些新装的node程序的安装路径和没有引入n之前的早期node的安装路径并不相同，因此我有理由猜测可能是crontab错误地采用了之前的v0.10.30版本的node程序。

为了验证猜想，我们可以在crontab中执行一个新的脚本：
{% code%}
* * * * * env > /tmp/env.output
{% endcode %}
这段脚本的作用是定时将crontab执行的环境变量输出到/tmp/env.output这个文件中去，待crontab脚本生效片刻之后，我们就能找到这个文件了，具体内容如下：
{% code %}
HOME=/home/melon
LOGNAME=melon
PATH=/usr/bin:/bin
LANG=en_US.UTF-8
SHELL=/bin/sh
PWD=/home/melon
{% endcode %}
到这里，我们应该都明白了。看PATH变量，很显然crontab默认的环境变量范围非常有限，只有/usr/bin和/bin下的程序，原来版本的node可执行文件是在/usr/bin下的，而通过n版本管理程序安装的node软件是装在/usr/local/bin下的，这样之前的猜想就能验证了。

那如何解决这个问题呢？<br>
1.可以在crontab脚本里把程序的路径写成绝对路径，比如不写node xxx.js而是/usr/local/bin/node xxx.js<br>
2.可以在要执行的bin或bash脚本开头自定义PATH变量，或者干脆直接在crontab文件开头自定义PATH变量，将PATH变量改动应用到所有定时脚本<br>
具体的大家可以看参考资料，或者自行google更多的资料

- References:
    1. [Reasons why crontab does not work](http://askubuntu.com/questions/23009/reasons-why-crontab-does-not-work)