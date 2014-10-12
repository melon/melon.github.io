title: "iptables和netfilter"
date: 2014-08-18 15:10
categories:
- IT
tags:
- linux
- security
---
在[《Iptables的一些概念》](/blog/2014/08/17/some-iptables-concepts/)里简单介绍了一些iptables的基本概念，继续研究，发现iptables其实只是一个应用层级别的数据包操作规则的配置软件。iptables配置的生效最终是靠Linux内核中的一个叫netfilter的框架来实现的。掌握netfilter的一些基本概念有助于理解iptables的用法。

netfilter是Linux内核中的一个框架/模块，提供了诸多灵活的网络相关的操作，例如数据包过滤(packet filtering)，网络地址转换(network address translation)，端口转换(port translation)等操作。netfilter提供了一系列的钩子(hook)，从而使得使用者在不同的阶段自定义规则成为可能。

下图描述了数据包经过netfilter框架的过程，和[《Iptables的一些概念》](/blog/2014/08/17/some-iptables-concepts/)中的引入的那张图很像
<pre>
--->[1]--->[ROUTE]--->[3]--->[4]--->
             |            ^
             |            |
             |         [ROUTE]
             v            |
            [2]          [5]
             |            ^
             |            |
             v            |
</pre>
数据包由左边进来，经过简单的检查之后，就会传给第一个钩子<strong>NF_IP_PRE_ROUTING [1]</strong><br>
然后就会进入路由的逻辑，以决定该数据包是供给本地程序用，还是转发到别的接口上<br>
如果是给本地进程用，那么在这之前，首先会调用钩子<strong>NF_IP_LOCAL_IN [2]</strong><br>
如果是给别的接口用的，就会调用钩子<strong>NF_IP_FORWARD [3]</strong><br>
最后在传入网络之前，最后一次会调用钩子<strong>NF_IP_POST_ROUTING [4]</strong><br>
对于由本地程序/进程产生的向外传送的数据包，首先会经过钩子<strong>NF_IP_LOCAL_OUT [5]</strong>的调用，然后再和转发那条支路一样，在传入网络之前，最后一次会调用钩子<strong>NF_IP_POST_ROUTING [4]</strong><br>

其实上文提到的钩子调用的地方，正是iptables的规则应用的地方。

<pre>
--->PRE------>[ROUTE]--->FWD---------->POST------>
   Conntrack    |       Mangle   ^    Mangle
   Mangle       |       Filter   |    NAT (Src)
   NAT (Dst)    |                |    Conntrack
   (QDisc)      |             [ROUTE]
                v                |
                IN Filter       OUT Conntrack
                |  Conntrack     ^  Mangle
                |  Mangle        |  NAT (Dst)
                v                |  Filter
</pre>

- References:
  1. [3. Netfilter Architecture](http://www.netfilter.org/documentation/HOWTO/netfilter-hacking-HOWTO-3.html)
  2. [Netfilter](http://en.wikipedia.org/wiki/Netfilter)