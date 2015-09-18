title: tcpdump入门用法举例
date: 2015-09-05 23:26:59
categories:
- IT
tags:
- tcpdump
- network
- tcp
- ip
---
我们在平时的开发过程中会遇到很多网络相关的问题，这些情况下如若能正确地使用tcpdump指令，找到问题症结的效率可能就会大大提高。

tcpdump是linux下比较常用的一项用来抓取分析网络流量包的命令。相比wireshark等一些可视化的流量抓包工具，tcpdump因其极大的可定制性而深受一些高级的网络分析工程师的喜欢。

初步理解tcpdump主要在于理解两个方面，一个是tcpdump的各种附加参数的使用方法，另一个是对tcpdump输出的理解。

## tcpdump基础用法举例

tcpdump命令在使用时，后面可以跟两部分东西，一部分是各种选项的指定（-v，-i等等），另一部分是过滤器（filters）的指定（例如host 105.111.111.111）下面给了一些常用的例子

{% code lang:bash %}
# 不要把地址和端口转成名字，verbose
tcpdump -nv
# 不要把地址和端口转成名字，very verbose
tcpdump -nvv
# 只抓取网络接口eth0的流量
tcpdump -i eth0
# 将抓取的结果同时用hex和ascii的方式展示
tcpdump -XX
# 将抓取的结果同时只用ascii的方式展示（比较实用）
tcpdump -A
{% endcode %}

{% code lang:bash %}
# 只抓取本机和105.111.111.111之间的流量
tcpdump -nv host 105.111.111.111
# 只抓取端口22的流量
tcpdump -nvv port 22
# 只抓取本机和105.111.111.111的22端口之间的流量
tcpdump -nv 'host 105.111.111.111 and port 22'
{% endcode %}

## tcpdump结果的格式说明

这是一条tcpdump返回的结果

<!--more-->

{% code lang:bash %}
19:19:41.101846 IP (tos 0x0, ttl 64, id 59613, offset 0, flags [DF], proto TCP (6), length 64)
    192.168.199.186.52745 > 58.83.142.136.80: Flags [S], cksum 0xe913 (correct), seq 2318769782, win 65535, options [mss 1460,nop,wscale 5,nop,nop,TS val 807523715 ecr 0,sackOK,eol], length 0
{% endcode %}

">"左边的表示数据包来源地址和端口，右边表示数据包目的地址和端口，Flags [S]表示数据包类型，后面的暂时不细说了

其中数据包类型有以下几种

- [S] - SYN (Start Connection)
- [.] - No Flag Set
- [P] - PSH (Push Data)
- [F] - FIN (Finish Connection)
- [R] - RST (Reset Connection)

在有些版本里，也能看到 [S.] 类型的数据包，它表示 SYN-ACK 数据包

## TCP三次握手

TCP的三次握手建立连接过程是一个比较重要的概念，它指的是以下三个过程：

1. 请求者发送 SYN 数据包
2. 响应者响应 SYN-ACK 数据包
3. 请求者再响应 ACK 数据包

我们可以通过tcpdump来具体地分析这三个过程，首先我们执行以下tcpdump命令

{% code lang:bash %}
sudo tcpdump -nv -A host meituan.com
{% endcode %}

然后在另一个终端里执行

{% code lang:bash %}
curl meituan.com
{% endcode %}

回到tcpdump的终端里，我们就能看到

{% code lang:bash %}
tcpdump: data link type PKTAP
tcpdump: listening on pktap, link-type PKTAP (Packet Tap), capture size 65535 bytes


00:37:34.328137 IP (tos 0x0, ttl 64, id 36931, offset 0, flags [DF], proto TCP (6), length 64)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [S], cksum 0x9c28 (correct), seq 4111302103, win 65535, options [mss 1460,nop,wscale 5,nop,nop,TS val 826523594 ecr 0,sackOK,eol], length 0
}..........(.............7....:S...S.P.
1C..........
00:37:34.334184 IP (tos 0x0, ttl 51, id 36931, offset 0, flags [DF], proto TCP (6), length 64)
    58.83.142.135.80 > 192.168.199.186.58707: Flags [S.], cksum 0x9636 (correct), seq 1795854595, ack 4111302104, win 65535, options [mss 1440,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,nop,sackOK,eol], length 0
`....4........E..@.C@.3.f7:S.......P.Sk
}......6..........................
00:37:34.334257 IP (tos 0x0, ttl 64, id 9865, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [.], cksum 0x09fe (correct), ack 1, win 65535, length 0
}.k...`....4..E..(&.@.@..	....:S...S.P.
..P...	...
00:37:34.334347 IP (tos 0x0, ttl 64, id 33194, offset 0, flags [DF], proto TCP (6), length 115)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [P.], cksum 0x4bea (correct), seq 1:76, ack 1, win 65535, length 75
}.k...`....4..E..s..@.@.g.....:S...S.P.
..P...K...GET / HTTP/1.1
Host: meituan.com
User-Agent: curl/7.43.0
Accept: */*


00:37:34.338982 IP (tos 0x0, ttl 51, id 7577, offset 0, flags [DF], proto TCP (6), length 40)
    58.83.142.135.80 > 192.168.199.186.58707: Flags [.], cksum 0xd0aa (correct), ack 76, win 14600, length 0
`....4........E..(..@.3...:S.......P.Sk
~#P.9.....
00:37:34.339364 IP (tos 0x0, ttl 51, id 7578, offset 0, flags [DF], proto TCP (6), length 511)
    58.83.142.135.80 > 192.168.199.186.58707: Flags [P.], cksum 0x5bee (correct), seq 1:472, ack 76, win 14600, length 471
`....4........E.....@.3..!:S.......P.Sk
~#P.9.[...HTTP/1.1 301 Moved Permanently
Server: Tengine
Date: Sat, 05 Sep 2015 16:37:32 GMT
Content-Type: text/html
Content-Length: 278
Connection: keep-alive
Location: http://www.meituan.com/

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<h1>301 Moved Permanently</h1>
<p>The requested resource has been assigned a new permanent URI.</p>
<hr/>Powered by Tengine</body>
</html>

00:37:34.339417 IP (tos 0x0, ttl 64, id 14523, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [.], cksum 0x07dc (correct), ack 472, win 65535, length 0
~#k...`....4..E..(8.@.@.......:S...S.P.
..P.......
00:37:34.339566 IP (tos 0x0, ttl 64, id 25304, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [F.], cksum 0x07db (correct), seq 76, ack 472, win 65535, length 0
~#k...`....4..E..(b.@.@.......:S...S.P.
..P.......
00:37:34.344616 IP (tos 0x0, ttl 51, id 7579, offset 0, flags [DF], proto TCP (6), length 40)
    58.83.142.135.80 > 192.168.199.186.58707: Flags [.], cksum 0xced2 (correct), ack 77, win 14600, length 0
`....4........E..(..@.3...:S.......P.Sk
~$P.9.....
00:37:34.344639 IP (tos 0x0, ttl 64, id 25465, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [.], cksum 0x07db (correct), ack 472, win 65535, length 0
~$k...`....4..E..(cy@.@.......:S...S.P.
..P.......
00:37:34.344811 IP (tos 0x0, ttl 51, id 7580, offset 0, flags [DF], proto TCP (6), length 40)
    58.83.142.135.80 > 192.168.199.186.58707: Flags [F.], cksum 0xced1 (correct), seq 472, ack 77, win 14600, length 0
`....4........E..(..@.3...:S.......P.Sk
~$P.9.....
00:37:34.344819 IP (tos 0x0, ttl 64, id 57586, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.199.186.58707 > 58.83.142.135.80: Flags [.], cksum 0x07da (correct), ack 473, win 65535, length 0
~$k...`....4..E..(..@.@.......:S...S.P.
..P.......
{% endcode %}

返回中有很多...的部分表示的是发送的具体数据，是因为加了-A的缘故才会返回这些内容

我们可以看到首先请求端发了个 Flags [S] 类型的数据包，即 SYN 数据包，然后服务器端响应了个 Flags [S.] 类型的数据包，即 SYN-ACK 数据包，来表示服务器端已经接收到 SYN 数据包，第三步请求端又发了个 ACK 数据包（虽然是Flags [.]，但应该表示的是ACK数据包），表示已经收到服务器端发来的 SYN-ACK 数据包。至此，请求端与服务器端之间的TCP连接就成功建立了。

然后请求端就发了个 Flags [P.] 类型的数据包，表示具体的请求数据。服务器端接受到之后，也返回了 Flags [P.] 类型的数据包，表示响应的具体数据。

## 总结

通过上面的简单描述，我估计大家对tcpdump就有了个初步的了解，想要继续深入了解tcpdump的可以参考下面的引文。

- References:
  1. [A Quick and Practical Reference for tcpdump](http://bencane.com/2014/10/13/quick-and-practical-reference-for-tcpdump/)
  2. [TCPDUMP](http://www.tcpdump.org/tcpdump_man.html)
  3. [A tcpdump Primer with Examples](https://danielmiessler.com/study/tcpdump/)
