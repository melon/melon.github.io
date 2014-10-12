title: "ping域名和ping IP时速度不同的原因"
date: 2014-08-07 10:35
categories:
- IT
tags:
- linux
---
不知道大家在ping的时候有没有遇到过这样的问题：
当你ping一个域名的时候，ping结果返回得很慢，但是如果直接ping这个域名的ip，结果却快很多。

直接ping ip的时候，每两次发包之间没有明显的能感知出来的延迟：

PING www.example.com (xxx.xxx.xxx.xxx) 56(84) bytes of data.<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=1 ttl=50 time=3.71 ms<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=2 ttl=50 time=5.61 ms<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=3 ttl=50 time=3.78 ms<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=4 ttl=50 time=4.11 ms<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=5 ttl=50 time=3.74 ms<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=6 ttl=50 time=3.64 ms<br><br>
--- www.example.com ping statistics ---<br>
6 packets transmitted, 6 received, 0% packet loss, time 25073ms<br>
rtt min/avg/max/mdev = 3.647/4.103/5.618/0.697 ms<br>


ping域名的时候，每两次发包间有明显的延迟：

PING www.example.com (xxx.xxx.xxx.xxx) 56(84) bytes of data.<br>
(10s...)<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=1 ttl=50 time=3.71 ms<br>
(3s...)<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=2 ttl=50 time=5.61 ms<br>
(4s...)<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=3 ttl=50 time=3.78 ms<br>
(6s...)<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=4 ttl=50 time=4.11 ms<br>
(3s...)<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=5 ttl=50 time=3.74 ms<br>
(5s...)<br>
64 bytes from xxx.xxx.xxx.xxx: icmp_req=6 ttl=50 time=3.64 ms<br><br>
--- www.example.com ping statistics ---<br>
6 packets transmitted, 6 received, 0% packet loss, time 25073ms<br>
rtt min/avg/max/mdev = 3.647/4.103/5.618/0.697 ms<br>

google相关资料后发现，当每次ping完得到响应之后，ping程序会尝试一次反向dns查询（reverse dns lookup）来获取“64 bytes from”后面的域名，如果查询速度很慢的话，就会给人似乎延迟很大的感觉，其实这也是ping感觉慢，但是每次ping的响应时间却并不慢的原因。

其实，ping指令有一个 -n 选项，加上之后可以阻止ping程序去进行反向dns查询，这样ping起来就“快”了！