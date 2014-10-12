title: "iptables的一些概念"
date: 2014-08-17 21:22
categories:
- IT
tags:
- linux
- security
---
之前研究用pptp在VPS上搭建vpn的时候，考虑到安全问题，用iptables禁用了大部分端口，只留出常用的80, 443, 22以及pptp所要用到的默认的1723端口，网络上关于这类的傻瓜式的教程也非常多，所以也没太细究iptables的工作原理。近来发现pptp搭的vpn被墙干扰得厉害，几乎都连不上，网上查了相关的资料也说pptp极不安全，所以今天研究了一下用基于IpSec的l2tp技术来搭建vpn。

l2tp本身并不特别复杂，无非是安装几个程序，但是一旦加上了防火墙，问题就变得有些复杂了。网上对于l2tp下的iptables设置五花八门，非常混乱，最后总算搞定，用iphone测试了一下，能连接上，而且能成功加载网页。实际上，l2tp一旦连接上之后，由于数据都是加密的，所以使用过程中几乎不会被侦测到关键词和被重置，但是问题是，l2tp的连接成功率非常低，连过几次之后，后来几乎无法连接成功了。因此，l2tp也几乎说是被功陷了。但是借着这个“机缘”，对iptables的一些机理产生了兴趣，略微去研究了一下。

iptables是一个相对来说比较深奥的东西，要想彻底搞明白它，要求你对网络技术有比较深入的认识，像互联网原理、网络协议之类的东西，很有必要知道些一二。

网络上数据的传播，无非就是通过数据包(packet)这个小单元来实现的，iptables所操作的对象就是这些数据包。通过对数据包的动向进行管控，很大程度上就意味着实现了防火墙的功能。iptables主要有三种类型的操作：mangle, filter, nat.

mangle主要是用来修改一些数据包的信息，便于后续处理，像更改数据包的TOS(Type of Service)、更改数据包的TTL等，一般来说个人感觉这个功能用得不是很多。

filter是实现防火墙主要功能的一个比较常用的功能，像禁用端口之类的功能都能通过filter来实现，大部分时候iptables只用filter就能实现基本需求了。

nat是Network Address Table的简称，当你的服务器身处多个网络之中（包括私有网络）时，就可以利用nat功能来让数据包在不同网络中穿梭，实现类似路由的功能。

以前在配置iptables的时候，只用到filter，今天在研究l2tp的时候，就接触到了nat.

知道了三种操作之后，我们再来了解一下数据包的走向，因为iptables之所以存在就是操作是为了控制数据包的走向。一个数据包首先会从远程的网络来到你主机的网卡上，之后就会有两种情况了，一是这个数据包会继续“深入”，直到抵达到需要用这个数据包的进程/程序那里去，另一种是这个数据包只是“路过”一下你这台机器，然后就走了。。数据包还有第三种走向就是从某个进程/程序出来，离开这台主机，进入网络中去。之前说的三种操作其实就是在这三种数据包走向的途中拦截或放行数据包的。

附上一张iptables数据流走向图：

![iptables数据流走向图](https://www.frozentux.net/iptables-tutorial/chunkyhtml/images/tables_traverse.jpg)

- References:
  1. [IP Tables Primer](http://bodhizazen.net/Tutorials/iptables)
  2. [Chapter 6. Traversing of tables and chains](https://www.frozentux.net/iptables-tutorial/chunkyhtml/c962.html)