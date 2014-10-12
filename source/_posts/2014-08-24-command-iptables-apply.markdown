title: "iptables-apply：更安全的指令"
date: 2014-08-24 15:10
categories:
- IT
tags:
- linux
---
在从文件导入iptables规则的时候，我们习惯了用iptables-restore指令

<pre>
sudo iptables-restore < /etc/iptables.rules
</pre>

也就是说将/etc/iptables.rules中的规则应用，使之生效。这在大多数情况下是没有什么问题的，但有些时候，比如我们设想一下，某一天，因为安全或者什么其他原因，你希望更改远程登录程序ssh的端口，比如将22端口改成了222端口。于是你从容自得地把ssh服务的配置文件/etc/ssh/sshd_config里的Port参数从22改成了222，然后熟练地重启sshd服务/etc/init.d/ssh restart，然后，当前ssh连接就断了。你也不会惊讶为什么断了，因为你知道改端口之后需要重新连接。你把ssh客户端的连接端口也改好之后，尝试再次登录。于是你就发现，怎么都连不上了。

忽的，你好像明白了什么。因为你用iptables禁用了大部分端口，只留了常用的一些端口，刚才改22端口的时候，你压根忘了还要改iptables规则。现在，一切都晚了。如果是国内的机房，还好办，亲自去机房跑一趟吧。如果是买的国外的VPS什么的，如果厂商没有应对这类情况的人性化措施的话，你就几乎没机会再次连上你的主机了。你的主机变成了一个“密室”。。。

这个时候，就能体现iptables-apply这个指令的好处了。iptables-apply的功能和iptables-restore一样，都是生效规则，而不同的就是iptables-apply只是“尝试”应用新规则，在你输入

<pre>
sudo iptables-apply /etc/iptables.rules
</pre>

之后，新规则会临时生效，同时，终端会输出一段问句让你来确认

<pre>
Applying new ruleset... done.
Can you establish NEW connections to the machine? (y/N)
</pre>

只有你输入"y"回车之后，指令才能真正“永久”生效。当然，如果这个时候因为更改规则之后导致你“失联”了，你就压根看不到这句话，更无从谈起输入"y"了。过了一段时间之后，iptables就会自动回退到旧规则，那样你就能再次连接了。

其实这种机制能让我们联想到很多类似的东西。

例如，在修改服务器网络相关的配置的时候，比较谨慎一点的做法，在修改相关配置或其他东西之前，先备个份，然后用crontab指令将备份的旧配置在一定时间之后重新生效一下。如果更改网络设置导致无法远程连接，因为crontab的定时任务，过一段时间之后就能恢复到之前的状态，因此就不会导致“严重的后果”。如果新的网络设置没有问题，就可以在crontab定时任务生效之前将它删掉即可。