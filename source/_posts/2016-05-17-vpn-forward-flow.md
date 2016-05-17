---
title: 搭建VPN中转服务器
date: 2016-05-17 18:39:10
categories:
- IT
tags:
- vpn
- iptables
- route
---
家里用的垃圾的宽带通服务，宽带质量实在没法恭维。晚上高峰时期下载速度估计只有1Mbps，然而打广告时却标榜100Mbps，虽然有心理准备根本达不到这么高的带宽，可谁曾想到会如此之慢。不过，这些都忍了，最让人无法接受的是这破宽带竟然连不到linode主机上。。我试过在其他运营商的宽带下是可以访问的。

最终，在暂时不换运营商的前提下，我找了一台国内中转服务器，使用haproxy来转发流量连接到linode。然而这种方式电脑用起来挺方便的，但是对iPhone等移动设备却不那么友好，众所周知，iPhone**的最好方法是使用VPN。为了手上的iPhone也能利用上我花钱买的两台主机，最近研究了一下使用国内中转服务器来转发VPN至海外服务器。

下面我讲一下主要思路，具体的步骤可以参考引文。

这个搭建过程主要有以下几个步骤：

- 在国内主机上安装OpenVPN服务端
- 在国内主机上安装ShadowVPN客户端
- 在国外主机上安装ShadowVPN服务端
- 在国内主机上将OpenVPN接受的流量转发到ShadowVPN
- 最后就是在苹果设备上使用OpenVPN客户端来连接国内主机了（当然电脑也可以安装相关软件连接）

References:
- [https://gist.github.com/ihciah/883068c5d1beb499a016](https://gist.github.com/ihciah/883068c5d1beb499a016)
- [https://github.com/OkamiSupport/VPN-traffic-redirect-to-another-vpn-tunnel](https://github.com/OkamiSupport/VPN-traffic-redirect-to-another-vpn-tunnel)
- [https://www.digitalocean.com/community/tutorials/how-to-install-openvpn-access-server-on-ubuntu-12-04](https://www.digitalocean.com/community/tutorials/how-to-install-openvpn-access-server-on-ubuntu-12-04)
